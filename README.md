# UP42 API walktrough

## Introduction

The UP42 API allows for doing the following things on a given project:

1. Work with jobs: 
   * get job(s)
   * cancel job
   * create & run job
   * get job output (`data.json`)
   * get job output directory
2. Work with workflows:
   * get workflows
   * get workflow
   * update workflow
   * create workflow
3. Work with jobs and tasks:
   * get job task logs
   * get job task outputs (`data.json`)
   * get job tasks output directory
   
It means that a **project key** is **always** needed. Therefore you
always need to create a project **through the UI**.

In the examples below I will use a project I have, so it won't work
for you, you should adapt to use a project of yours.

**Note**: Please be aware that the project ID and the project key
allow anyone to manipulate your project (account) so be careful and do
not share it around. Someone might find it and besides messing with
your project it will make you incur costs and thus reduce the
currently available credits in your platform.

## Requirements

 1. [`cURL`](https://curl.haxx.se).
 2. [`jq`](https://stedolan.github.io/jq/).

We will be using the
[Bash](https://en.wikipedia.org/wiki/Bash_(Unix_shell)) shell in this guide.


## Authentication: getting the token

Before attempting to do anything with the API you need to get a token
so that you perform any type of operation on your project.

```bash
# Set the project ID.
PROJ=5a21eaff-cdaa-48ab-bedf-5454116d16ff
# Set the project key.
PKEY=aoiTOv31.hab0M74qT9cB7K57wO6ue1glddcL3t5zsxb
# Get the token.
PTOKEN=$(curl -sX POST "https://$PROJ:$PKEY@api.up42.dev/oauth/token"   -H 'Content-Type: application/x-www-form-urlencoded' -d 'grant_type=client_credentials' | jq -r '.data.accessToken')
```
Now we can echo the token back in the shell:

```bash
echo $PTOKEN
eyJ0eXAiOiJKV1QiLCJraWQiOiIxIiwidG9rZW5fdHlwZSI6IkFDQ0VTUyIsImFsZyI6IlJTNTEyIn0.eyJpc3MiOiJiYWNrZW5kLWNvcmUiLCJqdGkiOiI5ZGYyMzY3MC02NDRkLTRkMGEtYTFlNi1hODIwN2QxZGQwNDgiLCJpYXQiOjE1NjE3MTc0ODcsInN1YiI6IjVhMjFlYWZmLWNkYWEtNDhhYi1iZWRmLTU0NTQxMTZkMTZmZiIsImF1ZCI6IjVhMjFlYWZmLWNkYWEtNDhhYi1iZWRmLTU0NTQxMTZkMTZmZiIsImV4cCI6MTU2MTcxNzc4NywiYXV0aG9yaXRpZXMiOlsiUlVOX0pPQiIsIlZJRVdfUFJPSkVDVCJdfQ.DLEUuifHzksf_Q_ReMF0aQXY-MOoy_nDu-noCGu7F8_Z2dBEJXbKILcvTB1t7ABVZmnd2eGlLiBuAF5zuz-L7nGuxqqzPawYy4GMB_ICc7HTuicYnx3fOGakby6qUGRuWlOmPGbcsgS_tRbt4pcjOPMvK0LbBXKobZb1HZYMdns4wiKVHE6IEyWn57k0eVm_y5fKImLIvGbqz060AakIamQ6O9uAHADOZwej9rnbkQO9e5LqP3hbb59sluyOhke0hYuJqA5VhssX743xxa3MZpxBRRhwR5YG_oxWEdOShhFq7T9S5i8fCZvhuoR3eQSkakTEfIMxLYQfDcycdptHJqXN5twtlYJ0hKTKuW0ezgELeTHtuSobg3xbZW7M8opX7lqtnnsVPVApo19ndqdaJtfTFiU1WgcveS0o47sXkPVtB7ohug420g5ux3XRCxgAY6vFHlvNWZZP6F6bSh-Ah7Gqm5jsW76DrloZyedOVz2qVoFU6XCicyXEsBSuo0giRlVHnVtRmqmHbTvyxFjndTbsoahxSH2rKX4H1AWjIyw_jEcZGBx4XZG2dWPYSNOR1SCx59i4XL9BzTVywjxNt50MpV92eIRI7doNSK-UXo6DClrXPl8-VskJrS_fTjyK-qD8P1tCHYs8eytnfKG0BZwrlhYAVYMHumvOtxxG0NE
```

This token is valid for **5** minutes. To get a new token repeat the
cURL request above. 

## Working with jobs

### List all the jobs for a given project

```bash
curl -L -s -H "Authorization: Bearer $PTOKEN" "https://api.up42.dev/projects/$PROJ/jobs" | jq > jobs_$PROJ.json
```

This creates the following
`jobs_5a21eaff-cdaa-48ab-bedf-5454116d16ff.json` file:


```js
{
  "error": null,
  "data": [
    {
      "id": "99722655-100d-477d-af1d-6907f7cba55e",
      "displayId": "99722655",
      "createdAt": "2019-05-27T07:51:41.514Z",
      "updatedAt": "2019-05-27T07:52:25.996Z",
      "status": "FAILED",
      "startedAt": "2019-05-27T07:51:41Z",
      "finishedAt": "2019-05-27T07:52:25Z",
      "inputs": {
        "oneatlas-pleiades-aoiclipped:1": {
          "bbox": null,
          "time": "2018-09-01T00:00:00+00:00/2018-10-10T00:00:00+00:00",
          "limit": 5,
          "intersects": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  13.1942067871,
                  32.8598251965
                ],
                [
                  13.1942067871,
                  32.8589787671
                ],
                [
                  13.1935800781,
                  32.8589787671
                ],
                [
                  13.1935800781,
                  32.8598251965
                ],
                [
                  13.1942067871,
                  32.8598251965
                ]
              ]
            ]
          },
          "zoom_level": 18
        }
      },
      "mode": "DEFAULT",
      "tasks": [
        {
          "id": "831e0add-eec3-4fb4-a944-7374a41b89f8",
          "displayId": "831e0add",
          "createdAt": "2019-05-27T07:51:41.515Z",
          "updatedAt": "2019-05-27T07:52:25.997Z",
          "name": "oneatlas-pleiades-aoiclipped:1",
          "block": {
            "id": "f026874d-e95e-4293-b811-7667130e054d",
            "createdAt": "2019-02-20T16:21:31.526Z",
            "updatedAt": "2019-05-27T07:01:50.428Z",
            "name": "oneatlas-pleiades-aoiclipped",
            "displayName": "Pléiades AOI-Clipped",
            "description": "Pléiades 1A/1B are very-high resolution twin satellites offering 0.5m resolution products. The constellation is highly reactive and has been designed for daily revisit anywhere on Earth,  with stereo and tristereo capacity.\n\nThis block provides pansharpened Pleiades HR imagery clipped to all tiles intersecting a given bounding box or AOI on a given zoom level. The part of the image that does not intersect with these tiles will be black. The block outputs a single GeoTIFF file and will store the AOI within the output feature geometry. The available output bands of the Pleiades block are: red, green, and blue. Pansharpened Pleiades HR imagery has a spatial resolution of 0.5x0.5m.",
            "containerUrl": "registry.up42.dev/marketplace/oneatlas-pleiades-aoiclipped:3mwS6nT31qrgA7FFkKbNnZxSdFILlT2hzkiLqJf6",
            "inputCapabilities": [],
            "outputCapabilities": [
              {
                "name": "up42.data.aoiclipped"
              }
            ],
            "provider": "OneAtlas",
            "tags": [
              "Pléiades",
              "Airbus",
              "very-high resolution",
              "global",
              "OneAtlas",
              "high revisit",
              "optical"
            ],
            "isPublic": true,
            "isValid": true,
            "parameters": {
              "bbox": {
                "type": "string",
                "default": null
              },
              "time": {
                "type": "dateRange",
                "default": null
              },
              "limit": {
                "type": "number",
                "default": 1
              },
              "intersects": {
                "type": "geometry"
              },
              "zoom_level": {
                "type": "number",
                "default": 18
              }
            },
            "isRunnableByUser": true,
            "type": "DATA",
            "isDryRunSupported": true,
            "availableVersions": [
              "0.0.1",
              "0.9.1",
              "0.9.11",
              "0.9.12",
              "0.9.13",
              "0.9.14",
              "0.9.15",
              "0.9.16",
              "0.9.17",
              "0.9.19",
              "0.9.2",
              "0.9.20",
              "0.9.22",
              "0.9.24",
              "0.9.25",
              "0.9.26",
              "0.9.27",
              "0.9.28",
              "0.9.29",
              "0.9.3",
              "0.9.30",
              "0.9.31",
              "0.9.33",
              "0.9.35",
              "0.9.4",
              "0.9.5",
              "0.9.6",
              "0.9.7",
              "0.9.8",
              "0.9.9",
              "1.0.0",
              "1.0.1",
              "1.0.10",
              "1.0.11",
              "1.0.2",
              "1.0.3",
              "1.0.4",
              "1.0.5",
              "1.0.6",
              "1.0.7",
              "1.0.8",
              "1.0.9",
              "1.1.10",
              "1.1.11",
              "1.1.12",
              "1.1.2",
              "1.1.3",
              "1.1.4",
              "1.1.5",
              "1.1.6",
              "1.1.7",
              "1.1.8",
              "1.1.9"
            ],
            "version": "1.1.5",
            "metadata": {
              "id": "fa956bb4-29cc-4eca-86c3-b12ecc93d8ff",
              "createdAt": "2019-05-27T07:01:50.429Z",
              "updatedAt": "2019-05-27T07:01:50.429Z",
              "overview": "Pléiades 1A/1B are very-high resolution twin satellites offering 0.5m resolution products. The constellation is highly reactive and has been designed for daily revisit anywhere on Earth,  with stereo and tristereo capacity.\n\nThis block provides pan-sharpened Pleiades HR imagery clipped to all tiles intersecting a given bounding box or AOI on a given zoom level. The part of the image that does not intersect with these tiles will be black. The block outputs a single GeoTIFF file and will store the AOI within the output feature geometry. The available output bands of the Pleiades block are: red, green, and blue (current version does not support NIR). Pan-sharpened Pleiades HR imagery has a spatial resolution of 0.5x0.5m.\n\n**Technical information**\n\n|Feature|Info|\n|:-|:-|\n|Archive date / refresh frequency | Archives available starting from 2016 (2014 over top 3000 cities). Full Earth accessible each day. |\n|Frequency bands | Panchromatic: 0.470-0.830 µm<br>Blue: 0.430-0.550 µm<br>Green: 0.500-0.620 µm<br>Red: 0.590-0.710 µm|\n|Resolution|Panchromatic: 0.5m<br>Multispectral: 2m<br>Swath: 20km at nadir|\n|Processing level|Ortho Bundle 12bits Reflectance JPEG2000 (Level3)<br>Ortho Bundle 8 bits Display GeoTIFF (Level3)|\n|Data format| GeoTIFF / JPEG2000 & DIMAP format|\n\n**Use cases**\n\nFor more information about potential use cases, see the [use cases gallery](https://oneatlas.airbus.com/case-study/).\n\n**More information**\n\nFor more information about this data, please see the [provider website](https://oneatlas.airbus.com/)\n",
              "termsAndConditionsUrl": "https://metadata.up42.com/OneAtlas/r13117_9_eula-pleiades-vuk-october2017.pdf",
              "iconUrl": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/0_Pleiades_1A-1B_Avatar.jpg",
              "pricingStrategy": {
                "id": "2d257425-e213-45cf-80c0-f833058a9b0f",
                "createdAt": "2019-05-27T07:01:50.430Z",
                "updatedAt": "2019-05-27T07:01:50.430Z",
                "type": "MEGABYTE_OUTPUT",
                "credits": 100,
                "displayId": "2d257425"
              },
              "blockMarketplaceSampleData": [
                {
                  "id": "abb40355-5cf1-4e65-a039-6c0ce1425926",
                  "createdAt": "2019-05-27T07:01:50.430Z",
                  "updatedAt": "2019-05-27T07:01:50.430Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_China_State_of_Jin.jpg",
                  "type": "IMAGE",
                  "displayId": "abb40355"
                },
                {
                  "id": "3fe49a4f-8203-433f-b2b0-db1cf4a3c4b1",
                  "createdAt": "2019-05-27T07:01:50.431Z",
                  "updatedAt": "2019-05-27T07:01:50.431Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_Egypt_Cairo.jpg",
                  "type": "IMAGE",
                  "displayId": "3fe49a4f"
                },
                {
                  "id": "df077d0e-18ff-4ff5-87a6-788901c756cb",
                  "createdAt": "2019-05-27T07:01:50.432Z",
                  "updatedAt": "2019-05-27T07:01:50.432Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_French_Polynesia_Tupai_Atoll.jpg",
                  "type": "IMAGE",
                  "displayId": "df077d0e"
                },
                {
                  "id": "e59077a0-0b5e-4cd1-b35c-a32f5c80fff1",
                  "createdAt": "2019-05-27T07:01:50.433Z",
                  "updatedAt": "2019-05-27T07:01:50.433Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_Japan_Cycle_Tree.jpg",
                  "type": "IMAGE",
                  "displayId": "e59077a0"
                },
                {
                  "id": "0e09cfb7-6d03-419e-ab36-099e52f5b48a",
                  "createdAt": "2019-05-27T07:01:50.433Z",
                  "updatedAt": "2019-05-27T07:01:50.433Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_US_Los_Angeles.jpg",
                  "type": "IMAGE",
                  "displayId": "0e09cfb7"
                }
              ],
              "displayId": "fa956bb4"
            },
            "machineName": "MEDIUM"
          },
          "status": "FAILED",
          "parents": [],
          "startedAt": "2019-05-27T07:51:41Z",
          "finishedAt": "2019-05-27T07:52:24Z"
        }
      ],
      "workflow": {
        "id": "17509261-9018-4a9b-9493-ccb2404455f7",
        "displayId": "17509261",
        "createdAt": "2019-05-27T07:50:03.998Z",
        "updatedAt": "2019-05-28T11:12:18.845Z",
        "name": "Pleiades multiple scenes"
      }
    },
    {
      "id": "5b7719e4-e992-4b05-a2fb-16df93184f29",
      "displayId": "5b7719e4",
      "createdAt": "2019-05-27T08:03:57.052Z",
      "updatedAt": "2019-05-27T08:04:33.164Z",
      "status": "FAILED",
      "startedAt": "2019-05-27T08:03:57Z",
      "finishedAt": "2019-05-27T08:04:33Z",
      "inputs": {
        "oneatlas-pleiades-aoiclipped:1": {
          "bbox": null,
          "time": "2018-09-01T00:00:00+00:00/2018-10-10T00:00:00+00:00",
          "limit": 2,
          "intersects": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  13.1942067871,
                  32.8598251965
                ],
                [
                  13.1942067871,
                  32.8589787671
                ],
                [
                  13.1935800781,
                  32.8589787671
                ],
                [
                  13.1935800781,
                  32.8598251965
                ],
                [
                  13.1942067871,
                  32.8598251965
                ]
              ]
            ]
          },
          "zoom_level": 18
        }
      },
      "mode": "DEFAULT",
      "tasks": [
        {
          "id": "f5281a0b-fde8-4cd5-8a59-859bfe20ad93",
          "displayId": "f5281a0b",
          "createdAt": "2019-05-27T08:03:57.053Z",
          "updatedAt": "2019-05-27T08:04:33.165Z",
          "name": "oneatlas-pleiades-aoiclipped:1",
          "block": {
            "id": "f026874d-e95e-4293-b811-7667130e054d",
            "createdAt": "2019-02-20T16:21:31.526Z",
            "updatedAt": "2019-05-27T07:01:50.428Z",
            "name": "oneatlas-pleiades-aoiclipped",
            "displayName": "Pléiades AOI-Clipped",
            "description": "Pléiades 1A/1B are very-high resolution twin satellites offering 0.5m resolution products. The constellation is highly reactive and has been designed for daily revisit anywhere on Earth,  with stereo and tristereo capacity.\n\nThis block provides pansharpened Pleiades HR imagery clipped to all tiles intersecting a given bounding box or AOI on a given zoom level. The part of the image that does not intersect with these tiles will be black. The block outputs a single GeoTIFF file and will store the AOI within the output feature geometry. The available output bands of the Pleiades block are: red, green, and blue. Pansharpened Pleiades HR imagery has a spatial resolution of 0.5x0.5m.",
            "containerUrl": "registry.up42.dev/marketplace/oneatlas-pleiades-aoiclipped:3mwS6nT31qrgA7FFkKbNnZxSdFILlT2hzkiLqJf6",
            "inputCapabilities": [],
            "outputCapabilities": [
              {
                "name": "up42.data.aoiclipped"
              }
            ],
            "provider": "OneAtlas",
            "tags": [
              "Pléiades",
              "Airbus",
              "very-high resolution",
              "global",
              "OneAtlas",
              "high revisit",
              "optical"
            ],
            "isPublic": true,
            "isValid": true,
            "parameters": {
              "bbox": {
                "type": "string",
                "default": null
              },
              "time": {
                "type": "dateRange",
                "default": null
              },
              "limit": {
                "type": "number",
                "default": 1
              },
              "intersects": {
                "type": "geometry"
              },
              "zoom_level": {
                "type": "number",
                "default": 18
              }
            },
            "isRunnableByUser": true,
            "type": "DATA",
            "isDryRunSupported": true,
            "availableVersions": [
              "0.0.1",
              "0.9.1",
              "0.9.11",
              "0.9.12",
              "0.9.13",
              "0.9.14",
              "0.9.15",
              "0.9.16",
              "0.9.17",
              "0.9.19",
              "0.9.2",
              "0.9.20",
              "0.9.22",
              "0.9.24",
              "0.9.25",
              "0.9.26",
              "0.9.27",
              "0.9.28",
              "0.9.29",
              "0.9.3",
              "0.9.30",
              "0.9.31",
              "0.9.33",
              "0.9.35",
              "0.9.4",
              "0.9.5",
              "0.9.6",
              "0.9.7",
              "0.9.8",
              "0.9.9",
              "1.0.0",
              "1.0.1",
              "1.0.10",
              "1.0.11",
              "1.0.2",
              "1.0.3",
              "1.0.4",
              "1.0.5",
              "1.0.6",
              "1.0.7",
              "1.0.8",
              "1.0.9",
              "1.1.10",
              "1.1.11",
              "1.1.12",
              "1.1.2",
              "1.1.3",
              "1.1.4",
              "1.1.5",
              "1.1.6",
              "1.1.7",
              "1.1.8",
              "1.1.9"
            ],
            "version": "1.1.5",
            "metadata": {
              "id": "fa956bb4-29cc-4eca-86c3-b12ecc93d8ff",
              "createdAt": "2019-05-27T07:01:50.429Z",
              "updatedAt": "2019-05-27T07:01:50.429Z",
              "overview": "Pléiades 1A/1B are very-high resolution twin satellites offering 0.5m resolution products. The constellation is highly reactive and has been designed for daily revisit anywhere on Earth,  with stereo and tristereo capacity.\n\nThis block provides pan-sharpened Pleiades HR imagery clipped to all tiles intersecting a given bounding box or AOI on a given zoom level. The part of the image that does not intersect with these tiles will be black. The block outputs a single GeoTIFF file and will store the AOI within the output feature geometry. The available output bands of the Pleiades block are: red, green, and blue (current version does not support NIR). Pan-sharpened Pleiades HR imagery has a spatial resolution of 0.5x0.5m.\n\n**Technical information**\n\n|Feature|Info|\n|:-|:-|\n|Archive date / refresh frequency | Archives available starting from 2016 (2014 over top 3000 cities). Full Earth accessible each day. |\n|Frequency bands | Panchromatic: 0.470-0.830 µm<br>Blue: 0.430-0.550 µm<br>Green: 0.500-0.620 µm<br>Red: 0.590-0.710 µm|\n|Resolution|Panchromatic: 0.5m<br>Multispectral: 2m<br>Swath: 20km at nadir|\n|Processing level|Ortho Bundle 12bits Reflectance JPEG2000 (Level3)<br>Ortho Bundle 8 bits Display GeoTIFF (Level3)|\n|Data format| GeoTIFF / JPEG2000 & DIMAP format|\n\n**Use cases**\n\nFor more information about potential use cases, see the [use cases gallery](https://oneatlas.airbus.com/case-study/).\n\n**More information**\n\nFor more information about this data, please see the [provider website](https://oneatlas.airbus.com/)\n",
              "termsAndConditionsUrl": "https://metadata.up42.com/OneAtlas/r13117_9_eula-pleiades-vuk-october2017.pdf",
              "iconUrl": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/0_Pleiades_1A-1B_Avatar.jpg",
              "pricingStrategy": {
                "id": "2d257425-e213-45cf-80c0-f833058a9b0f",
                "createdAt": "2019-05-27T07:01:50.430Z",
                "updatedAt": "2019-05-27T07:01:50.430Z",
                "type": "MEGABYTE_OUTPUT",
                "credits": 100,
                "displayId": "2d257425"
              },
              "blockMarketplaceSampleData": [
                {
                  "id": "abb40355-5cf1-4e65-a039-6c0ce1425926",
                  "createdAt": "2019-05-27T07:01:50.430Z",
                  "updatedAt": "2019-05-27T07:01:50.430Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_China_State_of_Jin.jpg",
                  "type": "IMAGE",
                  "displayId": "abb40355"
                },
                {
                  "id": "3fe49a4f-8203-433f-b2b0-db1cf4a3c4b1",
                  "createdAt": "2019-05-27T07:01:50.431Z",
                  "updatedAt": "2019-05-27T07:01:50.431Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_Egypt_Cairo.jpg",
                  "type": "IMAGE",
                  "displayId": "3fe49a4f"
                },
                {
                  "id": "df077d0e-18ff-4ff5-87a6-788901c756cb",
                  "createdAt": "2019-05-27T07:01:50.432Z",
                  "updatedAt": "2019-05-27T07:01:50.432Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_French_Polynesia_Tupai_Atoll.jpg",
                  "type": "IMAGE",
                  "displayId": "df077d0e"
                },
                {
                  "id": "e59077a0-0b5e-4cd1-b35c-a32f5c80fff1",
                  "createdAt": "2019-05-27T07:01:50.433Z",
                  "updatedAt": "2019-05-27T07:01:50.433Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_Japan_Cycle_Tree.jpg",
                  "type": "IMAGE",
                  "displayId": "e59077a0"
                },
                {
                  "id": "0e09cfb7-6d03-419e-ab36-099e52f5b48a",
                  "createdAt": "2019-05-27T07:01:50.433Z",
                  "updatedAt": "2019-05-27T07:01:50.433Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_US_Los_Angeles.jpg",
                  "type": "IMAGE",
                  "displayId": "0e09cfb7"
                }
              ],
              "displayId": "fa956bb4"
            },
            "machineName": "MEDIUM"
          },
          "status": "FAILED",
          "parents": [],
          "startedAt": "2019-05-27T08:03:57Z",
          "finishedAt": "2019-05-27T08:04:32Z"
        }
      ],
      "workflow": {
        "id": "17509261-9018-4a9b-9493-ccb2404455f7",
        "displayId": "17509261",
        "createdAt": "2019-05-27T07:50:03.998Z",
        "updatedAt": "2019-05-28T11:12:18.845Z",
        "name": "Pleiades multiple scenes"
      }
    },
    {
      "id": "90c6a11e-7993-4c35-8a14-5feca281d8d0",
      "displayId": "90c6a11e",
      "createdAt": "2019-05-27T08:09:18.256Z",
      "updatedAt": "2019-05-27T08:09:54.201Z",
      "status": "FAILED",
      "startedAt": "2019-05-27T08:09:18Z",
      "finishedAt": "2019-05-27T08:09:54Z",
      "inputs": {
        "oneatlas-pleiades-aoiclipped:1": {
          "bbox": null,
          "time": "2018-09-01T00:00:00+00:00/2018-10-10T00:00:00+00:00",
          "limit": 1,
          "intersects": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  13.1942067871,
                  32.8598251965
                ],
                [
                  13.1942067871,
                  32.8589787671
                ],
                [
                  13.1935800781,
                  32.8589787671
                ],
                [
                  13.1935800781,
                  32.8598251965
                ],
                [
                  13.1942067871,
                  32.8598251965
                ]
              ]
            ]
          },
          "zoom_level": 18
        }
      },
      "mode": "DEFAULT",
      "tasks": [
        {
          "id": "c9b6197a-7a02-4e60-9c8e-53dfffb53491",
          "displayId": "c9b6197a",
          "createdAt": "2019-05-27T08:09:18.257Z",
          "updatedAt": "2019-05-27T08:09:54.202Z",
          "name": "oneatlas-pleiades-aoiclipped:1",
          "block": {
            "id": "f026874d-e95e-4293-b811-7667130e054d",
            "createdAt": "2019-02-20T16:21:31.526Z",
            "updatedAt": "2019-05-27T07:01:50.428Z",
            "name": "oneatlas-pleiades-aoiclipped",
            "displayName": "Pléiades AOI-Clipped",
            "description": "Pléiades 1A/1B are very-high resolution twin satellites offering 0.5m resolution products. The constellation is highly reactive and has been designed for daily revisit anywhere on Earth,  with stereo and tristereo capacity.\n\nThis block provides pansharpened Pleiades HR imagery clipped to all tiles intersecting a given bounding box or AOI on a given zoom level. The part of the image that does not intersect with these tiles will be black. The block outputs a single GeoTIFF file and will store the AOI within the output feature geometry. The available output bands of the Pleiades block are: red, green, and blue. Pansharpened Pleiades HR imagery has a spatial resolution of 0.5x0.5m.",
            "containerUrl": "registry.up42.dev/marketplace/oneatlas-pleiades-aoiclipped:3mwS6nT31qrgA7FFkKbNnZxSdFILlT2hzkiLqJf6",
            "inputCapabilities": [],
            "outputCapabilities": [
              {
                "name": "up42.data.aoiclipped"
              }
            ],
            "provider": "OneAtlas",
            "tags": [
              "Pléiades",
              "Airbus",
              "very-high resolution",
              "global",
              "OneAtlas",
              "high revisit",
              "optical"
            ],
            "isPublic": true,
            "isValid": true,
            "parameters": {
              "bbox": {
                "type": "string",
                "default": null
              },
              "time": {
                "type": "dateRange",
                "default": null
              },
              "limit": {
                "type": "number",
                "default": 1
              },
              "intersects": {
                "type": "geometry"
              },
              "zoom_level": {
                "type": "number",
                "default": 18
              }
            },
            "isRunnableByUser": true,
            "type": "DATA",
            "isDryRunSupported": true,
            "availableVersions": [
              "0.0.1",
              "0.9.1",
              "0.9.11",
              "0.9.12",
              "0.9.13",
              "0.9.14",
              "0.9.15",
              "0.9.16",
              "0.9.17",
              "0.9.19",
              "0.9.2",
              "0.9.20",
              "0.9.22",
              "0.9.24",
              "0.9.25",
              "0.9.26",
              "0.9.27",
              "0.9.28",
              "0.9.29",
              "0.9.3",
              "0.9.30",
              "0.9.31",
              "0.9.33",
              "0.9.35",
              "0.9.4",
              "0.9.5",
              "0.9.6",
              "0.9.7",
              "0.9.8",
              "0.9.9",
              "1.0.0",
              "1.0.1",
              "1.0.10",
              "1.0.11",
              "1.0.2",
              "1.0.3",
              "1.0.4",
              "1.0.5",
              "1.0.6",
              "1.0.7",
              "1.0.8",
              "1.0.9",
              "1.1.10",
              "1.1.11",
              "1.1.12",
              "1.1.2",
              "1.1.3",
              "1.1.4",
              "1.1.5",
              "1.1.6",
              "1.1.7",
              "1.1.8",
              "1.1.9"
            ],
            "version": "1.1.5",
            "metadata": {
              "id": "fa956bb4-29cc-4eca-86c3-b12ecc93d8ff",
              "createdAt": "2019-05-27T07:01:50.429Z",
              "updatedAt": "2019-05-27T07:01:50.429Z",
              "overview": "Pléiades 1A/1B are very-high resolution twin satellites offering 0.5m resolution products. The constellation is highly reactive and has been designed for daily revisit anywhere on Earth,  with stereo and tristereo capacity.\n\nThis block provides pan-sharpened Pleiades HR imagery clipped to all tiles intersecting a given bounding box or AOI on a given zoom level. The part of the image that does not intersect with these tiles will be black. The block outputs a single GeoTIFF file and will store the AOI within the output feature geometry. The available output bands of the Pleiades block are: red, green, and blue (current version does not support NIR). Pan-sharpened Pleiades HR imagery has a spatial resolution of 0.5x0.5m.\n\n**Technical information**\n\n|Feature|Info|\n|:-|:-|\n|Archive date / refresh frequency | Archives available starting from 2016 (2014 over top 3000 cities). Full Earth accessible each day. |\n|Frequency bands | Panchromatic: 0.470-0.830 µm<br>Blue: 0.430-0.550 µm<br>Green: 0.500-0.620 µm<br>Red: 0.590-0.710 µm|\n|Resolution|Panchromatic: 0.5m<br>Multispectral: 2m<br>Swath: 20km at nadir|\n|Processing level|Ortho Bundle 12bits Reflectance JPEG2000 (Level3)<br>Ortho Bundle 8 bits Display GeoTIFF (Level3)|\n|Data format| GeoTIFF / JPEG2000 & DIMAP format|\n\n**Use cases**\n\nFor more information about potential use cases, see the [use cases gallery](https://oneatlas.airbus.com/case-study/).\n\n**More information**\n\nFor more information about this data, please see the [provider website](https://oneatlas.airbus.com/)\n",
              "termsAndConditionsUrl": "https://metadata.up42.com/OneAtlas/r13117_9_eula-pleiades-vuk-october2017.pdf",
              "iconUrl": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/0_Pleiades_1A-1B_Avatar.jpg",
              "pricingStrategy": {
                "id": "2d257425-e213-45cf-80c0-f833058a9b0f",
                "createdAt": "2019-05-27T07:01:50.430Z",
                "updatedAt": "2019-05-27T07:01:50.430Z",
                "type": "MEGABYTE_OUTPUT",
                "credits": 100,
                "displayId": "2d257425"
              },
              "blockMarketplaceSampleData": [
                {
                  "id": "abb40355-5cf1-4e65-a039-6c0ce1425926",
                  "createdAt": "2019-05-27T07:01:50.430Z",
                  "updatedAt": "2019-05-27T07:01:50.430Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_China_State_of_Jin.jpg",
                  "type": "IMAGE",
                  "displayId": "abb40355"
                },
                {
                  "id": "3fe49a4f-8203-433f-b2b0-db1cf4a3c4b1",
                  "createdAt": "2019-05-27T07:01:50.431Z",
                  "updatedAt": "2019-05-27T07:01:50.431Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_Egypt_Cairo.jpg",
                  "type": "IMAGE",
                  "displayId": "3fe49a4f"
                },
                {
                  "id": "df077d0e-18ff-4ff5-87a6-788901c756cb",
                  "createdAt": "2019-05-27T07:01:50.432Z",
                  "updatedAt": "2019-05-27T07:01:50.432Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_French_Polynesia_Tupai_Atoll.jpg",
                  "type": "IMAGE",
                  "displayId": "df077d0e"
                },
                {
                  "id": "e59077a0-0b5e-4cd1-b35c-a32f5c80fff1",
                  "createdAt": "2019-05-27T07:01:50.433Z",
                  "updatedAt": "2019-05-27T07:01:50.433Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_Japan_Cycle_Tree.jpg",
                  "type": "IMAGE",
                  "displayId": "e59077a0"
                },
                {
                  "id": "0e09cfb7-6d03-419e-ab36-099e52f5b48a",
                  "createdAt": "2019-05-27T07:01:50.433Z",
                  "updatedAt": "2019-05-27T07:01:50.433Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_US_Los_Angeles.jpg",
                  "type": "IMAGE",
                  "displayId": "0e09cfb7"
                }
              ],
              "displayId": "fa956bb4"
            },
            "machineName": "MEDIUM"
          },
          "status": "FAILED",
          "parents": [],
          "startedAt": "2019-05-27T08:09:18Z",
          "finishedAt": "2019-05-27T08:09:53Z"
        }
      ],
      "workflow": {
        "id": "17509261-9018-4a9b-9493-ccb2404455f7",
        "displayId": "17509261",
        "createdAt": "2019-05-27T07:50:03.998Z",
        "updatedAt": "2019-05-28T11:12:18.845Z",
        "name": "Pleiades multiple scenes"
      }
    },
    {
      "id": "fb7e5686-2d47-4358-ade8-4c4c530325f8",
      "displayId": "fb7e5686",
      "createdAt": "2019-05-27T17:17:08.738Z",
      "updatedAt": "2019-05-27T17:17:30.174Z",
      "status": "SUCCEEDED",
      "startedAt": "2019-05-27T17:17:10Z",
      "finishedAt": "2019-05-27T17:17:30Z",
      "inputs": {
        "config": {
          "mode": "DRY_RUN"
        },
        "oneatlas-pleiades-aoiclipped:1": {
          "bbox": null,
          "time": "2018-09-01T00:00:00+00:00/2018-10-10T00:00:00+00:00",
          "limit": 5,
          "intersects": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  13.1942067871,
                  32.8598251965
                ],
                [
                  13.1942067871,
                  32.8589787671
                ],
                [
                  13.1935800781,
                  32.8589787671
                ],
                [
                  13.1935800781,
                  32.8598251965
                ],
                [
                  13.1942067871,
                  32.8598251965
                ]
              ]
            ]
          },
          "zoom_level": 18
        }
      },
      "mode": "DRY_RUN",
      "tasks": [
        {
          "id": "547c8571-0e12-4924-ba47-f3471df74506",
          "displayId": "547c8571",
          "createdAt": "2019-05-27T17:17:08.755Z",
          "updatedAt": "2019-05-27T17:17:24.775Z",
          "name": "oneatlas-pleiades-aoiclipped:1",
          "block": {
            "id": "f026874d-e95e-4293-b811-7667130e054d",
            "createdAt": "2019-02-20T16:21:31.526Z",
            "updatedAt": "2019-05-27T07:01:50.428Z",
            "name": "oneatlas-pleiades-aoiclipped",
            "displayName": "Pléiades AOI-Clipped",
            "description": "Pléiades 1A/1B are very-high resolution twin satellites offering 0.5m resolution products. The constellation is highly reactive and has been designed for daily revisit anywhere on Earth,  with stereo and tristereo capacity.\n\nThis block provides pansharpened Pleiades HR imagery clipped to all tiles intersecting a given bounding box or AOI on a given zoom level. The part of the image that does not intersect with these tiles will be black. The block outputs a single GeoTIFF file and will store the AOI within the output feature geometry. The available output bands of the Pleiades block are: red, green, and blue. Pansharpened Pleiades HR imagery has a spatial resolution of 0.5x0.5m.",
            "containerUrl": "registry.up42.dev/marketplace/oneatlas-pleiades-aoiclipped:3mwS6nT31qrgA7FFkKbNnZxSdFILlT2hzkiLqJf6",
            "inputCapabilities": [],
            "outputCapabilities": [
              {
                "name": "up42.data.aoiclipped"
              }
            ],
            "provider": "OneAtlas",
            "tags": [
              "Pléiades",
              "Airbus",
              "very-high resolution",
              "global",
              "OneAtlas",
              "high revisit",
              "optical"
            ],
            "isPublic": true,
            "isValid": true,
            "parameters": {
              "bbox": {
                "type": "string",
                "default": null
              },
              "time": {
                "type": "dateRange",
                "default": null
              },
              "limit": {
                "type": "number",
                "default": 1
              },
              "intersects": {
                "type": "geometry"
              },
              "zoom_level": {
                "type": "number",
                "default": 18
              }
            },
            "isRunnableByUser": true,
            "type": "DATA",
            "isDryRunSupported": true,
            "availableVersions": [
              "0.0.1",
              "0.9.1",
              "0.9.11",
              "0.9.12",
              "0.9.13",
              "0.9.14",
              "0.9.15",
              "0.9.16",
              "0.9.17",
              "0.9.19",
              "0.9.2",
              "0.9.20",
              "0.9.22",
              "0.9.24",
              "0.9.25",
              "0.9.26",
              "0.9.27",
              "0.9.28",
              "0.9.29",
              "0.9.3",
              "0.9.30",
              "0.9.31",
              "0.9.33",
              "0.9.35",
              "0.9.4",
              "0.9.5",
              "0.9.6",
              "0.9.7",
              "0.9.8",
              "0.9.9",
              "1.0.0",
              "1.0.1",
              "1.0.10",
              "1.0.11",
              "1.0.2",
              "1.0.3",
              "1.0.4",
              "1.0.5",
              "1.0.6",
              "1.0.7",
              "1.0.8",
              "1.0.9",
              "1.1.10",
              "1.1.11",
              "1.1.12",
              "1.1.2",
              "1.1.3",
              "1.1.4",
              "1.1.5",
              "1.1.6",
              "1.1.7",
              "1.1.8",
              "1.1.9"
            ],
            "version": "1.1.5",
            "metadata": {
              "id": "fa956bb4-29cc-4eca-86c3-b12ecc93d8ff",
              "createdAt": "2019-05-27T07:01:50.429Z",
              "updatedAt": "2019-05-27T07:01:50.429Z",
              "overview": "Pléiades 1A/1B are very-high resolution twin satellites offering 0.5m resolution products. The constellation is highly reactive and has been designed for daily revisit anywhere on Earth,  with stereo and tristereo capacity.\n\nThis block provides pan-sharpened Pleiades HR imagery clipped to all tiles intersecting a given bounding box or AOI on a given zoom level. The part of the image that does not intersect with these tiles will be black. The block outputs a single GeoTIFF file and will store the AOI within the output feature geometry. The available output bands of the Pleiades block are: red, green, and blue (current version does not support NIR). Pan-sharpened Pleiades HR imagery has a spatial resolution of 0.5x0.5m.\n\n**Technical information**\n\n|Feature|Info|\n|:-|:-|\n|Archive date / refresh frequency | Archives available starting from 2016 (2014 over top 3000 cities). Full Earth accessible each day. |\n|Frequency bands | Panchromatic: 0.470-0.830 µm<br>Blue: 0.430-0.550 µm<br>Green: 0.500-0.620 µm<br>Red: 0.590-0.710 µm|\n|Resolution|Panchromatic: 0.5m<br>Multispectral: 2m<br>Swath: 20km at nadir|\n|Processing level|Ortho Bundle 12bits Reflectance JPEG2000 (Level3)<br>Ortho Bundle 8 bits Display GeoTIFF (Level3)|\n|Data format| GeoTIFF / JPEG2000 & DIMAP format|\n\n**Use cases**\n\nFor more information about potential use cases, see the [use cases gallery](https://oneatlas.airbus.com/case-study/).\n\n**More information**\n\nFor more information about this data, please see the [provider website](https://oneatlas.airbus.com/)\n",
              "termsAndConditionsUrl": "https://metadata.up42.com/OneAtlas/r13117_9_eula-pleiades-vuk-october2017.pdf",
              "iconUrl": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/0_Pleiades_1A-1B_Avatar.jpg",
              "pricingStrategy": {
                "id": "2d257425-e213-45cf-80c0-f833058a9b0f",
                "createdAt": "2019-05-27T07:01:50.430Z",
                "updatedAt": "2019-05-27T07:01:50.430Z",
                "type": "MEGABYTE_OUTPUT",
                "credits": 100,
                "displayId": "2d257425"
              },
              "blockMarketplaceSampleData": [
                {
                  "id": "abb40355-5cf1-4e65-a039-6c0ce1425926",
                  "createdAt": "2019-05-27T07:01:50.430Z",
                  "updatedAt": "2019-05-27T07:01:50.430Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_China_State_of_Jin.jpg",
                  "type": "IMAGE",
                  "displayId": "abb40355"
                },
                {
                  "id": "3fe49a4f-8203-433f-b2b0-db1cf4a3c4b1",
                  "createdAt": "2019-05-27T07:01:50.431Z",
                  "updatedAt": "2019-05-27T07:01:50.431Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_Egypt_Cairo.jpg",
                  "type": "IMAGE",
                  "displayId": "3fe49a4f"
                },
                {
                  "id": "df077d0e-18ff-4ff5-87a6-788901c756cb",
                  "createdAt": "2019-05-27T07:01:50.432Z",
                  "updatedAt": "2019-05-27T07:01:50.432Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_French_Polynesia_Tupai_Atoll.jpg",
                  "type": "IMAGE",
                  "displayId": "df077d0e"
                },
                {
                  "id": "e59077a0-0b5e-4cd1-b35c-a32f5c80fff1",
                  "createdAt": "2019-05-27T07:01:50.433Z",
                  "updatedAt": "2019-05-27T07:01:50.433Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_Japan_Cycle_Tree.jpg",
                  "type": "IMAGE",
                  "displayId": "e59077a0"
                },
                {
                  "id": "0e09cfb7-6d03-419e-ab36-099e52f5b48a",
                  "createdAt": "2019-05-27T07:01:50.433Z",
                  "updatedAt": "2019-05-27T07:01:50.433Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_US_Los_Angeles.jpg",
                  "type": "IMAGE",
                  "displayId": "0e09cfb7"
                }
              ],
              "displayId": "fa956bb4"
            },
            "machineName": "MEDIUM"
          },
          "status": "SUCCEEDED",
          "parents": [],
          "startedAt": "2019-05-27T17:17:10Z",
          "finishedAt": "2019-05-27T17:17:23Z"
        }
      ],
      "workflow": {
        "id": "17509261-9018-4a9b-9493-ccb2404455f7",
        "displayId": "17509261",
        "createdAt": "2019-05-27T07:50:03.998Z",
        "updatedAt": "2019-05-28T11:12:18.845Z",
        "name": "Pleiades multiple scenes"
      }
    },
    {
      "id": "c87f644f-e86e-4244-a533-98326911f30f",
      "displayId": "c87f644f",
      "createdAt": "2019-05-28T10:02:31.938Z",
      "updatedAt": "2019-05-28T10:03:07.684Z",
      "status": "FAILED",
      "startedAt": "2019-05-28T10:02:32Z",
      "finishedAt": "2019-05-28T10:03:07Z",
      "inputs": {
        "oneatlas-pleiades-aoiclipped:1": {
          "bbox": null,
          "time": "2018-09-01T00:00:00+00:00/2018-10-10T00:00:00+00:00",
          "limit": 1,
          "intersects": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  13.1942067871,
                  32.8598251965
                ],
                [
                  13.1942067871,
                  32.8589787671
                ],
                [
                  13.1935800781,
                  32.8589787671
                ],
                [
                  13.1935800781,
                  32.8598251965
                ],
                [
                  13.1942067871,
                  32.8598251965
                ]
              ]
            ]
          },
          "zoom_level": 18
        }
      },
      "mode": "DEFAULT",
      "tasks": [
        {
          "id": "aa05d162-0807-4508-8dc1-941e40bb1815",
          "displayId": "aa05d162",
          "createdAt": "2019-05-28T10:02:31.938Z",
          "updatedAt": "2019-05-28T10:03:07.684Z",
          "name": "oneatlas-pleiades-aoiclipped:1",
          "block": {
            "id": "f026874d-e95e-4293-b811-7667130e054d",
            "createdAt": "2019-02-20T16:21:31.526Z",
            "updatedAt": "2019-05-27T07:01:50.428Z",
            "name": "oneatlas-pleiades-aoiclipped",
            "displayName": "Pléiades AOI-Clipped",
            "description": "Pléiades 1A/1B are very-high resolution twin satellites offering 0.5m resolution products. The constellation is highly reactive and has been designed for daily revisit anywhere on Earth,  with stereo and tristereo capacity.\n\nThis block provides pansharpened Pleiades HR imagery clipped to all tiles intersecting a given bounding box or AOI on a given zoom level. The part of the image that does not intersect with these tiles will be black. The block outputs a single GeoTIFF file and will store the AOI within the output feature geometry. The available output bands of the Pleiades block are: red, green, and blue. Pansharpened Pleiades HR imagery has a spatial resolution of 0.5x0.5m.",
            "containerUrl": "registry.up42.dev/marketplace/oneatlas-pleiades-aoiclipped:3mwS6nT31qrgA7FFkKbNnZxSdFILlT2hzkiLqJf6",
            "inputCapabilities": [],
            "outputCapabilities": [
              {
                "name": "up42.data.aoiclipped"
              }
            ],
            "provider": "OneAtlas",
            "tags": [
              "Pléiades",
              "Airbus",
              "very-high resolution",
              "global",
              "OneAtlas",
              "high revisit",
              "optical"
            ],
            "isPublic": true,
            "isValid": true,
            "parameters": {
              "bbox": {
                "type": "string",
                "default": null
              },
              "time": {
                "type": "dateRange",
                "default": null
              },
              "limit": {
                "type": "number",
                "default": 1
              },
              "intersects": {
                "type": "geometry"
              },
              "zoom_level": {
                "type": "number",
                "default": 18
              }
            },
            "isRunnableByUser": true,
            "type": "DATA",
            "isDryRunSupported": true,
            "availableVersions": [
              "0.0.1",
              "0.9.1",
              "0.9.11",
              "0.9.12",
              "0.9.13",
              "0.9.14",
              "0.9.15",
              "0.9.16",
              "0.9.17",
              "0.9.19",
              "0.9.2",
              "0.9.20",
              "0.9.22",
              "0.9.24",
              "0.9.25",
              "0.9.26",
              "0.9.27",
              "0.9.28",
              "0.9.29",
              "0.9.3",
              "0.9.30",
              "0.9.31",
              "0.9.33",
              "0.9.35",
              "0.9.4",
              "0.9.5",
              "0.9.6",
              "0.9.7",
              "0.9.8",
              "0.9.9",
              "1.0.0",
              "1.0.1",
              "1.0.10",
              "1.0.11",
              "1.0.2",
              "1.0.3",
              "1.0.4",
              "1.0.5",
              "1.0.6",
              "1.0.7",
              "1.0.8",
              "1.0.9",
              "1.1.10",
              "1.1.11",
              "1.1.12",
              "1.1.2",
              "1.1.3",
              "1.1.4",
              "1.1.5",
              "1.1.6",
              "1.1.7",
              "1.1.8",
              "1.1.9"
            ],
            "version": "1.1.5",
            "metadata": {
              "id": "fa956bb4-29cc-4eca-86c3-b12ecc93d8ff",
              "createdAt": "2019-05-27T07:01:50.429Z",
              "updatedAt": "2019-05-27T07:01:50.429Z",
              "overview": "Pléiades 1A/1B are very-high resolution twin satellites offering 0.5m resolution products. The constellation is highly reactive and has been designed for daily revisit anywhere on Earth,  with stereo and tristereo capacity.\n\nThis block provides pan-sharpened Pleiades HR imagery clipped to all tiles intersecting a given bounding box or AOI on a given zoom level. The part of the image that does not intersect with these tiles will be black. The block outputs a single GeoTIFF file and will store the AOI within the output feature geometry. The available output bands of the Pleiades block are: red, green, and blue (current version does not support NIR). Pan-sharpened Pleiades HR imagery has a spatial resolution of 0.5x0.5m.\n\n**Technical information**\n\n|Feature|Info|\n|:-|:-|\n|Archive date / refresh frequency | Archives available starting from 2016 (2014 over top 3000 cities). Full Earth accessible each day. |\n|Frequency bands | Panchromatic: 0.470-0.830 µm<br>Blue: 0.430-0.550 µm<br>Green: 0.500-0.620 µm<br>Red: 0.590-0.710 µm|\n|Resolution|Panchromatic: 0.5m<br>Multispectral: 2m<br>Swath: 20km at nadir|\n|Processing level|Ortho Bundle 12bits Reflectance JPEG2000 (Level3)<br>Ortho Bundle 8 bits Display GeoTIFF (Level3)|\n|Data format| GeoTIFF / JPEG2000 & DIMAP format|\n\n**Use cases**\n\nFor more information about potential use cases, see the [use cases gallery](https://oneatlas.airbus.com/case-study/).\n\n**More information**\n\nFor more information about this data, please see the [provider website](https://oneatlas.airbus.com/)\n",
              "termsAndConditionsUrl": "https://metadata.up42.com/OneAtlas/r13117_9_eula-pleiades-vuk-october2017.pdf",
              "iconUrl": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/0_Pleiades_1A-1B_Avatar.jpg",
              "pricingStrategy": {
                "id": "2d257425-e213-45cf-80c0-f833058a9b0f",
                "createdAt": "2019-05-27T07:01:50.430Z",
                "updatedAt": "2019-05-27T07:01:50.430Z",
                "type": "MEGABYTE_OUTPUT",
                "credits": 100,
                "displayId": "2d257425"
              },
              "blockMarketplaceSampleData": [
                {
                  "id": "abb40355-5cf1-4e65-a039-6c0ce1425926",
                  "createdAt": "2019-05-27T07:01:50.430Z",
                  "updatedAt": "2019-05-27T07:01:50.430Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_China_State_of_Jin.jpg",
                  "type": "IMAGE",
                  "displayId": "abb40355"
                },
                {
                  "id": "3fe49a4f-8203-433f-b2b0-db1cf4a3c4b1",
                  "createdAt": "2019-05-27T07:01:50.431Z",
                  "updatedAt": "2019-05-27T07:01:50.431Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_Egypt_Cairo.jpg",
                  "type": "IMAGE",
                  "displayId": "3fe49a4f"
                },
                {
                  "id": "df077d0e-18ff-4ff5-87a6-788901c756cb",
                  "createdAt": "2019-05-27T07:01:50.432Z",
                  "updatedAt": "2019-05-27T07:01:50.432Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_French_Polynesia_Tupai_Atoll.jpg",
                  "type": "IMAGE",
                  "displayId": "df077d0e"
                },
                {
                  "id": "e59077a0-0b5e-4cd1-b35c-a32f5c80fff1",
                  "createdAt": "2019-05-27T07:01:50.433Z",
                  "updatedAt": "2019-05-27T07:01:50.433Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_Japan_Cycle_Tree.jpg",
                  "type": "IMAGE",
                  "displayId": "e59077a0"
                },
                {
                  "id": "0e09cfb7-6d03-419e-ab36-099e52f5b48a",
                  "createdAt": "2019-05-27T07:01:50.433Z",
                  "updatedAt": "2019-05-27T07:01:50.433Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_US_Los_Angeles.jpg",
                  "type": "IMAGE",
                  "displayId": "0e09cfb7"
                }
              ],
              "displayId": "fa956bb4"
            },
            "machineName": "MEDIUM"
          },
          "status": "FAILED",
          "parents": [],
          "startedAt": "2019-05-28T10:02:32Z",
          "finishedAt": "2019-05-28T10:03:06Z"
        }
      ],
      "workflow": {
        "id": "17509261-9018-4a9b-9493-ccb2404455f7",
        "displayId": "17509261",
        "createdAt": "2019-05-27T07:50:03.998Z",
        "updatedAt": "2019-05-28T11:12:18.845Z",
        "name": "Pleiades multiple scenes"
      }
    },
    {
      "id": "91fd0fb7-226a-431f-8ec7-11ad23c5e056",
      "displayId": "91fd0fb7",
      "createdAt": "2019-05-28T11:13:16.492Z",
      "updatedAt": "2019-05-28T11:13:37.119Z",
      "status": "SUCCEEDED",
      "startedAt": "2019-05-28T11:13:17Z",
      "finishedAt": "2019-05-28T11:13:37Z",
      "inputs": {
        "oneatlas-pleiades-aoiclipped:1": {
          "bbox": null,
          "time": "2018-09-01T00:00:00+00:00/2018-10-10T00:00:00+00:00",
          "limit": 5,
          "intersects": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  13.1942067871,
                  32.8598251965
                ],
                [
                  13.1942067871,
                  32.8589787671
                ],
                [
                  13.1935800781,
                  32.8589787671
                ],
                [
                  13.1935800781,
                  32.8598251965
                ],
                [
                  13.1942067871,
                  32.8598251965
                ]
              ]
            ]
          },
          "zoom_level": 18
        }
      },
      "mode": "DEFAULT",
      "tasks": [
        {
          "id": "d86207cb-574e-4930-916d-3fe492b98b96",
          "displayId": "d86207cb",
          "createdAt": "2019-05-28T11:13:16.492Z",
          "updatedAt": "2019-05-28T11:13:31.431Z",
          "name": "oneatlas-pleiades-aoiclipped:1",
          "block": {
            "id": "f026874d-e95e-4293-b811-7667130e054d",
            "createdAt": "2019-02-20T16:21:31.526Z",
            "updatedAt": "2019-05-28T09:38:33.743Z",
            "name": "oneatlas-pleiades-aoiclipped",
            "displayName": "Pléiades AOI-Clipped",
            "description": "Pléiades 1A/1B are very-high resolution twin satellites offering 0.5m resolution products. The constellation is highly reactive and has been designed for daily revisit anywhere on Earth,  with stereo and tristereo capacity.\n\nThis block provides pansharpened Pleiades HR imagery clipped to all tiles intersecting a given bounding box or AOI on a given zoom level. The part of the image that does not intersect with these tiles will be black. The block outputs a single GeoTIFF file and will store the AOI within the output feature geometry. The available output bands of the Pleiades block are: red, green, and blue. Pansharpened Pleiades HR imagery has a spatial resolution of 0.5x0.5m.",
            "containerUrl": "registry.up42.dev/marketplace/oneatlas-pleiades-aoiclipped:xArSuk1GCXnjttpsx4jZamnMAWpC6yI0tQ6INUlq",
            "inputCapabilities": [],
            "outputCapabilities": [
              {
                "name": "up42.data.aoiclipped"
              }
            ],
            "provider": "OneAtlas",
            "tags": [
              "Pléiades",
              "Airbus",
              "very-high resolution",
              "global",
              "OneAtlas",
              "high revisit",
              "optical"
            ],
            "isPublic": true,
            "isValid": true,
            "parameters": {
              "bbox": {
                "type": "string",
                "default": null
              },
              "time": {
                "type": "dateRange",
                "default": null
              },
              "limit": {
                "type": "number",
                "default": 1
              },
              "intersects": {
                "type": "geometry"
              },
              "zoom_level": {
                "type": "number",
                "default": 18
              }
            },
            "isRunnableByUser": true,
            "type": "DATA",
            "isDryRunSupported": true,
            "availableVersions": [
              "0.0.1",
              "0.9.1",
              "0.9.11",
              "0.9.12",
              "0.9.13",
              "0.9.14",
              "0.9.15",
              "0.9.16",
              "0.9.17",
              "0.9.19",
              "0.9.2",
              "0.9.20",
              "0.9.22",
              "0.9.24",
              "0.9.25",
              "0.9.26",
              "0.9.27",
              "0.9.28",
              "0.9.29",
              "0.9.3",
              "0.9.30",
              "0.9.31",
              "0.9.33",
              "0.9.35",
              "0.9.4",
              "0.9.5",
              "0.9.6",
              "0.9.7",
              "0.9.8",
              "0.9.9",
              "1.0.0",
              "1.0.1",
              "1.0.10",
              "1.0.11",
              "1.0.2",
              "1.0.3",
              "1.0.4",
              "1.0.5",
              "1.0.6",
              "1.0.7",
              "1.0.8",
              "1.0.9",
              "1.1.10",
              "1.1.11",
              "1.1.12",
              "1.1.2",
              "1.1.3",
              "1.1.4",
              "1.1.5",
              "1.1.6",
              "1.1.7",
              "1.1.8",
              "1.1.9"
            ],
            "version": "1.1.6",
            "metadata": {
              "id": "b51d2850-edf2-4919-b99c-0507b329442e",
              "createdAt": "2019-05-28T09:38:33.754Z",
              "updatedAt": "2019-05-28T09:38:33.754Z",
              "overview": "Pléiades 1A/1B are very-high resolution twin satellites offering 0.5m resolution products. The constellation is highly reactive and has been designed for daily revisit anywhere on Earth,  with stereo and tristereo capacity.\n\nThis block provides pan-sharpened Pleiades HR imagery clipped to all tiles intersecting a given bounding box or AOI on a given zoom level. The part of the image that does not intersect with these tiles will be black. The block outputs a single GeoTIFF file and will store the AOI within the output feature geometry. The available output bands of the Pleiades block are: red, green, and blue (current version does not support NIR). Pan-sharpened Pleiades HR imagery has a spatial resolution of 0.5x0.5m.\n\n**Technical information**\n\n|Feature|Info|\n|:-|:-|\n|Archive date / refresh frequency | Archives available starting from 2016 (2014 over top 3000 cities). Full Earth accessible each day. |\n|Frequency bands | Panchromatic: 0.470-0.830 µm<br>Blue: 0.430-0.550 µm<br>Green: 0.500-0.620 µm<br>Red: 0.590-0.710 µm|\n|Resolution|Panchromatic: 0.5m<br>Multispectral: 2m<br>Swath: 20km at nadir|\n|Processing level|Ortho Bundle 12bits Reflectance JPEG2000 (Level3)<br>Ortho Bundle 8 bits Display GeoTIFF (Level3)|\n|Data format| GeoTIFF / JPEG2000 & DIMAP format|\n\n**Use cases**\n\nFor more information about potential use cases, see the [use cases gallery](https://oneatlas.airbus.com/case-study/).\n\n**More information**\n\nFor more information about this data, please see the [provider website](https://oneatlas.airbus.com/)\n",
              "termsAndConditionsUrl": "https://metadata.up42.com/OneAtlas/r13117_9_eula-pleiades-vuk-october2017.pdf",
              "iconUrl": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/0_Pleiades_1A-1B_Avatar.jpg",
              "pricingStrategy": {
                "id": "7a892618-5496-41ba-a902-0efc46ad5f53",
                "createdAt": "2019-05-28T09:38:33.755Z",
                "updatedAt": "2019-05-28T09:38:33.755Z",
                "type": "MEGABYTE_OUTPUT",
                "credits": 100,
                "displayId": "7a892618"
              },
              "blockMarketplaceSampleData": [
                {
                  "id": "d5138725-7a65-4841-a6b9-13a5fc037ca4",
                  "createdAt": "2019-05-28T09:38:33.756Z",
                  "updatedAt": "2019-05-28T09:38:33.756Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_China_State_of_Jin.jpg",
                  "type": "IMAGE",
                  "displayId": "d5138725"
                },
                {
                  "id": "c8942193-aaa0-4803-a48d-c267ca98794a",
                  "createdAt": "2019-05-28T09:38:33.756Z",
                  "updatedAt": "2019-05-28T09:38:33.756Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_Egypt_Cairo.jpg",
                  "type": "IMAGE",
                  "displayId": "c8942193"
                },
                {
                  "id": "e5b97e5e-dceb-426c-a5f4-4505bb231db4",
                  "createdAt": "2019-05-28T09:38:33.757Z",
                  "updatedAt": "2019-05-28T09:38:33.757Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_French_Polynesia_Tupai_Atoll.jpg",
                  "type": "IMAGE",
                  "displayId": "e5b97e5e"
                },
                {
                  "id": "95995118-dcdb-4f03-a47c-79f25f120e94",
                  "createdAt": "2019-05-28T09:38:33.757Z",
                  "updatedAt": "2019-05-28T09:38:33.757Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_Japan_Cycle_Tree.jpg",
                  "type": "IMAGE",
                  "displayId": "95995118"
                },
                {
                  "id": "dfae1a2c-9c72-4662-b87c-441e0e07cd00",
                  "createdAt": "2019-05-28T09:38:33.757Z",
                  "updatedAt": "2019-05-28T09:38:33.757Z",
                  "value": "https://metadata.up42.com/OneAtlas/Pleiades_HR_AOI/Pleiades_1A-1B_US_Los_Angeles.jpg",
                  "type": "IMAGE",
                  "displayId": "dfae1a2c"
                }
              ],
              "displayId": "b51d2850"
            },
            "machineName": "MEDIUM"
          },
          "status": "SUCCEEDED",
          "parents": [],
          "startedAt": "2019-05-28T11:13:17Z",
          "finishedAt": "2019-05-28T11:13:30Z"
        }
      ],
      "workflow": {
        "id": "17509261-9018-4a9b-9493-ccb2404455f7",
        "displayId": "17509261",
        "createdAt": "2019-05-27T07:50:03.998Z",
        "updatedAt": "2019-05-28T11:12:18.845Z",
        "name": "Pleiades multiple scenes"
      }
    }
  ]
}

```

### Create and run a job

To create and run a job we need to get first the workflow IDs. 

```bash
WORKFLOW=$(curl -L -s -H "Authorization: Bearer $PTOKEN" "https://api.up42.dev/projects/$PROJ/jobs" | jq -r '.data[] | .workflow.id')
```

that returns a single element, since I only have one workflow for this
project: 

```bash
echo $WORKFLOW
21415975-390f-4215-becb-8d46aaf5156c
```

we also need to get the job parameters. In this case I'm just copying
from a previous job. Using the previously saved job list. We have:

```bash
cat jobs_5a21eaff-cdaa-48ab-bedf-5454116d16ff.json | jq '.data[0].inputs' > job_params_5a21eaff-cdaa-48ab-bedf-5454116d16ff.json
```

The first returned job parameters was returned.

```js
{
    "land_cover_classification:1": {
        "n_clusters": 6,
        "n_iterations": 10,
        "n_sieve_pixels": 64
    },
    "sentinelhub-landsat8-aoiclipped:1": {
        "bbox": null,
        "time": null,
        "limit": 1,
        "intersects": {
            "type": "Polygon",
            "coordinates": [
                [
                    [
                        -8.877645,
                        40.152078
                    ],
                    [
                        -8.871337,
                        40.139659
                    ],
                    [
                        -8.849105,
                        40.141048
                    ],
                    [
                        -8.860468,
                        40.152447
                    ],
                    [
                        -8.877645,
                        40.152078
                    ]
                ]
            ]
        },
        "zoom_level": 17
    }
}

```


