# AudioSet Download
Steps to download the AudioSet dataset with [yt-dlp](https://github.com/yt-dlp/yt-dlp).


## 1. Download the CSV files for dataset splits 

**NOTE**: For your convenience the csv files and label file can already be found in this repository, in the `downloads/audioset` folder. However, you may proceed with the step below if there are any updates to the dataset, or you want to download the full train_segments dataset.


The splits can be found on the official site [here](https://research.google.com/audioset/download.html). The CSV files are in the following format:

```
# YTID, start_seconds, end_seconds, positive_labels
-0RWZT-miFs, 420.000, 430.000, "/m/03v3yw,/m/0k4j"
```
The positive labels are the encoded version of the text labels, which can be downloaded [here](https://github.com/tensorflow/models/blob/master/research/audioset/yamnet/yamnet_class_map.csv).

## 2. Use the download script
A script to download the required audio segments have been prepared. We will be using `yt-dlp` to facilitate the downloading, as it enables us to download the videos within a specified timeframe. It is super helpful, as we will not need to download a whole 10+ minutes of footage just to slice 10 seconds of audio from it.

Please first install `yt-dlp` with this specified working version. (If the version below doesn't work anymore, download the latest one) I highly recommend creating a separate virtual environment for this.

```bash
# create a virtualenv and enter it
python3 -m venv ~/yt_venv
source ~/yt_venv/bin/activate

# install yt-dlp
python3 -m pip install --upgrade pip
python3 -m pip install tqdm yt-dlp==2022.7.18
```

Next, we run the download script. For other optional arguments, refer to the script itself.
```bash
cd downloads/audioset
# python3 download.py <output dir> <csv filepath> --num-threads <num threads>
python3 download.py /mnt/d/datasets/audioset balanced_train_segments.csv --num-threads 8 --create-manifest
```

This command will create 3 distinct items:

1. `downloaded` directory (where the raw segmented audio files are placed)
2. `processed` directory (where the formatted audio files are placed)
3. `manifest.json` (which will be used as input for the torch dataloaders later on)

The `manifest.json` file will have the following structure. It closely resembles [NVIDIA NeMo](https://docs.nvidia.com/deeplearning/nemo/user-guide/docs/en/stable/asr/datasets.html#librispeech)'s manifest format.
```python
# {'audio_filepath': <rel. path to audio file>, 'duration': <duration(s)>, 'labels': [label1, label2, ...]}
{"audio_filepath": "processed/Y--i-y1v8Hy8_0_9.wav", "duration": 9, "labels": ["/m/04rlf", "/m/09x0r", "/t/dd00004", "/t/dd00005"]}
{"audio_filepath": "processed/Y--U7joUcTCo_0_10.wav", "duration": 10, "labels": ["/m/01b_21"]}
```