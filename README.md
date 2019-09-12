# Radarr Custom Format Sync

[![docker-data](https://images.microbadger.com/badges/image/rubasace/radarr-custom-format-sync.svg)](https://microbadger.com/images/rubasace/radarr-folder-organizer "Get your own image badge on microbadger.com")
[![docker-commit](https://images.microbadger.com/badges/commit/rubasace/radarr-custom-format-sync.svg)](https://microbadger.com/images/rubasace/radarr-custom-format-sync "Get your own commit badge on microbadger.com")
[![docker-version](https://images.microbadger.com/badges/version/rubasace/radarr-custom-format-sync.svg)](https://microbadger.com/images/rubasace/radarr-custom-format-sync "Get your own version badge on microbadger.com")

## Description
Radarr Custom Format Sync checks Radarr history to correlate downloaded releases with the imported ones in order to put back all custom formats that might have been lost after the importing process.

## Why?
Radarr custom formats are very useful for use cases such as downloading a movie in original version as soon as it's available and upgrading it once a Dual version is released.
In order to work with trackers that don't have standardized namings, [Jackett](https://github.com/Jackett/Jackett "Jackett Github") is able to append some information such as language codes to the release name so then Radarr can pick the best release.

The problem is that any information that Jackett adds to the release name is not reflected on the imported release once it's downloaded and imported by Radarr as Radarr only uses the filename for assigning the custom formats. 

### Example: 

Assuming a custom format named ``Dual`` applied to movies that are both ``[spanish]`` and ``[english]`` and a release named ``DeadPool (2016)``:
1. Jackett finds ``DeadPool (2016)`` in a custom tracker and appends the language flags returning to Radarr the release name ``DeadPool (2016) [spanish] [english]``
2. Radarr decides to download ``DeadPool (2016) [spanish] [english]`` as it's the one that matches the ``Dual`` custom format
3. After the download is finished Radarr imports the release, but its release name is ``DeadPool (2016)`` so it doesn't add the ``Dual`` custom format to it.

This script solves this problem, checking the custom formats of each imported movie in the Radarr library and replacing it by the custom formats of the grabbed release instead.

## Configuration
### Example Config.txt:
```ini
[Radarr]
url = http://changeme.com:3333
key = 4384803r2808rvsvj023r9fjvvd0fjv3
```
### Configuration steps:
1. Edit the Config.txt file and replace your servers URL and API key for Radarr under the ``[Radarr]`` section.
2. Alternatively, if running the application with Docker, provide the values as the envars ``RADARR_URL``and ``RADARR_KEY`` (The ``Config.txt`` file will be generated automatically inside the container)

## How to Run
### Standalone
Recomended to run using cron every 15 minutes or an interval of your preference.
```bash
pip install -r requirements.txt
python CustomFormatSync.py
```
### Docker
```bash
docker run -d --name radarr-custom-format-sync \
        --restart=unless-stopped \
        -v /path/to/logs:/logs \
        -e RADARR_URL=yourRadarrUrl \
        -e RADARR_KEY=yourRadarrKey \
        -e DELAY=15m \
        rubasace/radarr-custom-format-sync
```
## Requirements
 * Python 3.4 or greater
 * Radarr server
