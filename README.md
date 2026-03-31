## Description
This tool allows fetching polly level0b data from the rsd2-server. 

## Requirements:
For fetching data from the API you have two options. In both cases you need a valid API-key. 
### Option 1
Use the file ```fetch_polly_data.py``` from this repo for fetching data - you only need the "requests" python-package in your python environment.

### Option 2 
You can use the API directly in combination with curl in a shell.

## Usage
### Option 1
Using ```fetch_polly_data.py``` for fetching data.
```
  -h, --help            show this help message and exit
  --site SITE           Site name
  --date DATE           Date in YYYYMMDD
  --api-key API_KEY     Use specific API key
  --download            Switch for downloading or just showing the results
  --download_dir DOWNLOAD_DIR
                        The folder to download to. Only necessary if download switch is activated
```

#### Example usage:
Checking availability of polly level0b data:
```
fetch_polly_data.py --site leipzig --date 20260326 --api-key 1234-abcd-5678
```
returns:
```
{
    "arielle": {
        "level0b_files": [
            "arielle/2026/2026_03_26_Thu_ARI_00_00_01.nc"
        ]
    }
}
```

The ```--download``` switch will activate the download of available files:
```
fetch_polly_data.py --site leipzig --date 20260326 --api-key 1234-abcd-5678 --download
```

### Option 2

Optionally you can set the directory to download to with ```--download_dir```.

```
curl -H "X-API-Key: $your-secret-API-key"  "https://rsd2.tropos.de/polly_api/api?&site=leipzig&date=20250626" | jq
```
This will return the available data in json style. If multiple devices were measuring at the specified site at the specified date, it will return multiple level0b files:

```
{
  "arielle": {
    "level0b_files": [
      "arielle/2025/2025_06_26_Thu_ARI_00_00_01.nc"
    ]
  },
  "pollyxt_fmi": {},
  "pollyxt_lacros": {
    "level0b_files": [
      "pollyxt_lacros/2025/2025_06_26_Thu_LACROS_00_00_01.nc"
    ]
  }
}
```
After that you can generate a temporary download-API-token (valid for a few minutes), by calling the following (note the /api? vs. /apitoken? address-snippet):
```
curl -H "X-API-Key: $your-secret-API-key"  "https://rsd2.tropos.de/polly_api/apitoken?&site=leipzig&date=20250626" | jq
```
This will show the temporary download tokens, e.g.:
```
{
  "download_tokens": [
    "eyJmaWxlbmFtZSI6Ii9kYXRhL2xldmVsMGIvcG9sbHkyNGgv...",
    "YcJyrVkrLzEnNS8xNVbJS0k9JLEnUz0ktS80xSNIvyM_JqTQ..."
  ]
}
```
Now you can download the desired file by typing:

```
curl -o polly_level0_20250626_leipzig.nc "https://rsd2.tropos.de/polly_api/download/eyJmaWxlbmFtZSI6Ii9kYXRhL2xldmVsMGIvcG9sbHkyNGgv..."
```

