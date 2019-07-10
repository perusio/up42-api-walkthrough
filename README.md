# UP42 API walktrough

## Introduction

The UP42 API allows for doing the following things on a given project:

1. Work with jobs: 
   * get job(s)
   * cancel job
   * create & run job
   * get job output (`data.json`)
   * get job output directory
2. Work with jobs and tasks:
   * get job task logs
   * get job task outputs (`data.json`)
   * get job tasks output directory 
3. Work with workflows:
   * get workflows
   * get workflow
   * update workflow
   * create workflow
    
It means that a **project key** is **always** needed. Therefore you
always need to create a project **through the UI**.

In the examples below I will use a project I have, so it won't work
for you, you should adapt to use a project of yours.

**Note**: Please be aware that the project ID and the project key
allow anyone to manipulate your project (account) so be careful and do
not share it around. Someone might find it and besides messing with
your project it will make you incur costs and thus reduce the
currently available credits in our platform. 

## Requirements

 1. [`cURL`](https://curl.haxx.se).
 2. [`jq`](https://stedolan.github.io/jq/).

[Bash](https://en.wikipedia.org/wiki/Bash_(Unix_shell)) is the shell
used in this guide.

All output is given as Github
[gists](https://help.github.com/en/articles/creating-gists#about-gists)
linked below as they are returned by the API.

## Authentication: getting the token

Before attempting to do anything with the API you need to get a token
in order to perform any type of operation on your project.

```bash
# Set the project ID.
PROJ=5a21eaff-cdaa-48ab-bedf-5454116d16ff
# Set the project key.
 PKEY=aoiTOv31.hab0M74qT9cB7K57wO6ue1glddcL3t5zsxb
# Get the token.
PTOKEN=$(curl -sX POST "https://$PROJ:$PKEY@api.up42.com/oauth/token"   -H 'Content-Type: application/x-www-form-urlencoded' -d 'grant_type=client_credentials' | jq -r '.data.accessToken')
```

Now you can echo the token in the shell:

```bash
echo $PTOKEN
eyJ0eXAiOiJKV1QiLCJraWQiOiIxIiwidG9rZW5fdHlwZSI6IkFDQ0VTUyIsImFsZyI6IlJTNTEyIn0.eyJpc3MiOiJiYWNrZW5kLWNvcmUiLCJqdGkiOiI5ZGYyMzY3MC02NDRkLTRkMGEtYTFlNi1hODIwN2QxZGQwNDgiLCJpYXQiOjE1NjE3MTc0ODcsInN1YiI6IjVhMjFlYWZmLWNkYWEtNDhhYi1iZWRmLTU0NTQxMTZkMTZmZiIsImF1ZCI6IjVhMjFlYWZmLWNkYWEtNDhhYi1iZWRmLTU0NTQxMTZkMTZmZiIsImV4cCI6MTU2MTcxNzc4NywiYXV0aG9yaXRpZXMiOlsiUlVOX0pPQiIsIlZJRVdfUFJPSkVDVCJdfQ.DLEUuifHzksf_Q_ReMF0aQXY-MOoy_nDu-noCGu7F8_Z2dBEJXbKILcvTB1t7ABVZmnd2eGlLiBuAF5zuz-L7nGuxqqzPawYy4GMB_ICc7HTuicYnx3fOGakby6qUGRuWlOmPGbcsgS_tRbt4pcjOPMvK0LbBXKobZb1HZYMdns4wiKVHE6IEyWn57k0eVm_y5fKImLIvGbqz060AakIamQ6O9uAHADOZwej9rnbkQO9e5LqP3hbb59sluyOhke0hYuJqA5VhssX743xxa3MZpxBRRhwR5YG_oxWEdOShhFq7T9S5i8fCZvhuoR3eQSkakTEfIMxLYQfDcycdptHJqXN5twtlYJ0hKTKuW0ezgELeTHtuSobg3xbZW7M8opX7lqtnnsVPVApo19ndqdaJtfTFiU1WgcveS0o47sXkPVtB7ohug420g5ux3XRCxgAY6vFHlvNWZZP6F6bSh-Ah7Gqm5jsW76DrloZyedOVz2qVoFU6XCicyXEsBSuo0giRlVHnVtRmqmHbTvyxFjndTbsoahxSH2rKX4H1AWjIyw_jEcZGBx4XZG2dWPYSNOR1SCx59i4XL9BzTVywjxNt50MpV92eIRI7doNSK-UXo6DClrXPl8-VskJrS_fTjyK-qD8P1tCHYs8eytnfKG0BZwrlhYAVYMHumvOtxxG0NE
```

This token is valid for **5** minutes. To get a new token repeat the
cURL request above. 

**Note**: Since Bash does not record in the shell history all commands
started with a space we recommend you set the `PKEY` variable above
such that the line start with a space like done here.

## Working with jobs

### List all the jobs for a given project

```bash
curl -L -s -H "Authorization: Bearer $PTOKEN" "https://api.up42.com/projects/$PROJ/jobs" | jq '.' > jobs_$PROJ.json
```

This creates the following
[`jobs_5a21eaff-cdaa-48ab-bedf-5454116d16ff.json`](https://gist.github.com/perusio/4e228693b0e0c10492d7ccc706d69a2a) file.


### Create and run a job

To create and run a job you need to get first the workflow IDs. 

```bash
WORKFLOW=$(curl -L -s -H "Authorization: Bearer $PTOKEN" "https://api.up42.com/projects/$PROJ/jobs" | jq -j '.data[0] | .workflow.id')
```

that returns a single element, since there is only one workflow for this
project: 

```bash
echo $WORKFLOW 21415975-390f-4215-becb-8d46aaf5156c
```

You also need to get the job parameters. In this case you are just copying
from a previous job. Using the previously saved job list.

```bash
cat jobs_5a21eaff-cdaa-48ab-bedf-5454116d16ff.json | jq '.data[0].inputs' > job_params_5a21eaff-cdaa-48ab-bedf-5454116d16ff.json
```

The first returned job parameters are:

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

Finally you can create and run the job:

```bash
# Create the URL as variable.
URL_POST_JOB="https://api.up42.com/projects/$PROJ/workflows/$WORKFLOW/jobs"
curl -s -L -X POST -H "Authorization: Bearer $PTOKEN" -H 'Content-Type: application/json' $URL_POST_JOB -d @job_params_5a21eaff-cdaa-48ab-bedf-5454116d16ff.json
```

You can see the job parameters [here](https://gist.github.com/perusio/fc948f4876897968e6d7e345f79ee0da).

### Get the previously launched job information

Now that a job is launched to obtain information on it you query the
API the following way:

```bash
# Job URL.
URL_JOB_INFO=https://api.up42.com/projects/$PROJ/jobs/$JOB"
# Variable with the job ID.
JOB=96b4c117-ab4d-44cf-afb1-0922d91031d4
curl -s -L -H "Authorization: Bearer $PTOKEN" $URL_JOB_INFO | jq '.' > jobs_job-$JOB.json
```

It returns the
[JSON](https://gist.github.com/perusio/e4e00cd7190ed97da3f25f78600c042e)
containing all the job information.

### Get the job status

Now filter the previous request to get the job status.

```bash
curl -L -s -H "Authorization: Bearer $PTOKEN"
"https://api.up42.com/projects/$PROJ/jobs/96b4c117-ab4d-44cf-afb1-0922d91031d4" | jq -r '.data.status'
```
In this case it returns:

```bash
RUNNING
```

This means that the job is still running.

### Get the jobs logs

To get the log of a running job you first need to identify the task
that is running. For that you use `jq` to query the previously
saved file containing the job information.

```bash
TASK=$(cat jobs_job-96b4c117-ab4d-44cf-afb1-0922d91031d4.json | jq -j '.data.tasks[] as $task | if $task.status == "RUNNING" then $task.id else "" end')
```

It returns:

```bash
echo $TASK
24c78a98-3def-4ee5-853d-2d5150757f2b
```

```bash
RUNNING_TASK_URL="https://api.up42.com/projects/$PROJ/jobs/$JOB/tasks/$TASK"
curl -s -L -H "Authorization: Bearer $PTOKEN" -H 'Content-Type: text/plain' "$RUNNING_TASK_URL/logs" > task_log-$TASK.txt
```

This command returns the log file available at [`https://gist.github.com/perusio/60639d67a47e241cdc8356d8c30a1ff9`](https://gist.github.com/perusio/60639d67a47e241cdc8356d8c30a1ff9).

### Get the job results

Once the job completes you can query the API to get the results.
Obtaining the [GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) file and/or the output directory delivered as a
[gzipped](https://en.wikipedia.org/wiki/Gzip) [tarball](https://en.wikipedia.org/wiki/Tar_(computing)).

#### Get the results: GeoJSON

```bash
OUTPUT_URL="https://api.up42.com/projects/$PROJ/jobs/$JOB/outputs
curl -s -L -H "Authorization: Bearer $PTOKEN" "$OUTPUT_URL/data-json"  | jq '.' > output-$JOB.json
```

Produces this [output](https://gist.github.com/perusio/4597361dc4792dfdda8a7260b39e9baf).


### Get the results: tarball

```bash
curl -s -L -H "Authorization: Bearer $PTOKEN" -o output-$JOB.tar.gz "$OUTPUT_URL/directory"
```

Inspect the retrieved tarball:

```bash
> tar tar ztvf output_$JOB.tar.gz

drwxrwxrwx  0 root   root        0 Jul  3 00:39 output
-rw-r--r--  0 root   root   432316 Jul  3 00:39 output/data.json
-rw-r--r--  0 root   root  5515635 Jul  3 00:39 output/e3650bac-bfbe-4ed2-bec4-9ea50245d2c0_land_cover.tif

```

There is both the GeoJSON file and the output as a
[GeoTIFF](https://en.wikipedia.org/wiki/GeoTIFF) file. The file name
is constructed from the first task ID and part of the block name. See
below for an explanation of what tasks are.

### Get individual tasks results

The job is composed of two tasks, each corresponding to a block in the
workflow: the first is obtaining the [Landsat 8](https://up42.com/marketplace/block/95519b2d-09d7-4cd0-a321-4d6a46bef6c1)
data, the second is runnning the [Land cover classification](https://up42.com/marketplace/block/ae2113e8-05df-41e6-9871-5d782705d8e1). We
can obtain the partial results, i.e., we can get the results from each
task in the job.

The task results are again given as a GeoJSON file and/or a tarball as
they are for a job result.

Iterating through the tasks in the job file.

```bash
cat jobs_job-$JOB.json | jq -r '.data.tasks[] | .id  + "_" + .name'
```

which outputs:

```bash
3344b712-aa9a-4cdb-94ae-7f3e379b7369 sentinelhub-landsat8-aoiclipped:1
24c78a98-3def-4ee5-853d-2d5150757f2b land_cover_classification:1
```

The first is the task ID and the second is the task name, clearly
identifying the task ID and what it corresponds to in terms of the
workflow.

Create two shell variables, one for each task:

```bash
TASK1=$(cat jobs_job-$JOB.json | jq -j '.data.tasks[0] | .id')
TASK2=$(cat jobs_job-$JOB.json | jq -j '.data.tasks[1] | .id')
```

```bash
> echo $TASK1 $TASK2

3344b712-aa9a-4cdb-94ae-7f3e379b7369 24c78a98-3def-4ee5-853d-2d5150757f2b
```

Now with the individual tasks IDs let us proceed to get the
respective results.

##### First task results: GeoJSON

The first task is the Landsat 8 data acquisition. The output GeoJSON
is:

```bash
curl -s -L -H "Authorization: Bearer $PTOKEN" "$TASK1_URL/outputs/data-json" | jq '.' > output_task-$TASK1.json
```

returning the following [file](https://gist.github.com/perusio/f9407da92c65a1bcb76621b658185ad6).


##### First task results: tarball

```bash
curl -s -H "Authorization: Bearer $PTOKEN" -o output_$TASK1.tar.gz "$TASK1_URL/outputs/directory"

```

Inspecting the tarball:

```bash
> tar ztvf output_$TASK1.tar.gz
drwxrwxrwx  0 root   root        0 Jul  3 00:23 output

-rw-r--r--  0 root   root    36197 Jul  3 00:23 output/data.json
-rw-r--r--  0 root   root 132209093 Jul  3 00:23 output/e3650bac-bfbe-4ed2-bec4-9ea50245d2c0.tif
```

you can see the resulting Landsat 8 GeoTIFF image there.

##### Second task results: GeoJSON


```bash
 curl -s -H "Authorization: Bearer $PTOKEN" "$TASK2_URL/outputs/data-json" | jq '.' > output_task-$TASK2.json
```

This will be the same GeoJSON as we got above for the job
results. They may look sintatically different, but semantically they
are the same, as you can confirm in this [gist](96b4c117-ab4d-44cf-afb1-0922d91031d4).


##### Second task results: tarball

Similarly for the tarball:

```bash
curl -s -H "Authorization: Bearer $PTOKEN" -o output_task-$TASK2.tar.gz "$TASK2_URL/outputs/directory"
```

```bash
> tar ztvf output_task-$TASK2.tar.gz

drwxrwxrwx  0 root   root        0 Jul  3 00:39 output
-rw-r--r--  0 root   root   432316 Jul  3 00:39 output/data.json
-rw-r--r--  0 root   root  5515635 Jul  3 00:39 output/e3650bac-bfbe-4ed2-bec4-9ea50245d2c0_land_cover.tif
```

As you can see the results are the same as for the job. Which means that:

> the final task of a workflow produces the same results as the job
> itself


## TODO

 1. Document the workflow API.
 2. Document the blocks API.
 3. Move this content to https://docs.up42.com.
