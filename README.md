# Image and Video Quality Studies on MTurk

Python scripts for publishing image/video quality-rating HITs on
**Amazon Mechanical Turk**, storing media in S3, and collecting submitted
ratings into MySQL.

## Original workflow

| Media | Input directory | Create HITs | Collect results |
| --- | --- | --- | --- |
| Images | `imm/images` | [imm/CreateImmHIT.py](imm/CreateImmHIT.py) | [imm/ResultsImm.py](imm/ResultsImm.py) |
| Videos | `vid/videos` | [vid/CreateVideoHIT.py](vid/CreateVideoHIT.py) | [vid/ResultsVideo.py](vid/ResultsVideo.py) |

The creation scripts request **five assignments per HIT**.
The image collector uses a minimum of **three submitted assignments** before
inserting results; the original video workflow uses the same threshold.
Cached URL/HIT lists support adding new media without recreating every previous
HIT. These caches are local pickle files, not a transactional job scheduler.

## Prerequisites

- A compatible **Python 2.7** environment with boto3 and PyMySQL.
- Your own MTurk requester access, authorized AWS configuration, and S3 resources.
- Your own image/video HIT layouts and type IDs.
- A development MySQL database configured for the source's connection settings.

Python 2 is end-of-life, and no dependency lock is supplied.
The original scripts are a historical research prototype.
Configure IDs through the legacy `Key.py` interface, but keep real credentials
out of tracked source. When adapting the project, prefer AWS's standard
credential providers and least-privilege permissions.

Update the hardcoded bucket names, media URL prefixes, and region assumptions
in the managers and result collectors. The original URLs are not resources
that a new user should reuse.

## Execution directories and side effects

Run image scripts with **`imm` as the working directory** and video scripts
with **`vid` as the working directory**. Include the repository root on
`PYTHONPATH` so the shared configuration module can be imported.
Keep each workflow's generated `.p` files together and load only trusted caches.

For example, after completing configuration, a sandbox image workflow in
PowerShell is:

```powershell
$env:PYTHONPATH = (Get-Location).Path
Push-Location imm
python2 CreateImmHIT.py
python2 ResultsImm.py
Pop-Location
```

The scripts run their workflows at module level; **importing them can also
perform uploads or API operations**. Sandbox is the default MTurk endpoint,
but S3 storage and production actions can cost money.
Review endpoint, reward, and assignment settings before execution.

## Database and supporting tools

[initDB.py](initDB.py) initializes `immquality` and `vidquality` in `dbmysql`,
with worker ID, media filename, quality rating, age, sex, and screen resolution.
**Running or importing it drops and recreates those tables.**
Never use it against existing study data without an approved backup/reset.

- [imm/imageManager.py](imm/imageManager.py) and
  [vid/videoManager.py](vid/videoManager.py): S3 uploads and media caches.
- [imm/DeleteImmHIT.py](imm/DeleteImmHIT.py) and
  [vid/DeleteVideoHIT.py](vid/DeleteVideoHIT.py): HIT lifecycle operations;
  review their effects before use.
- [Payment.py](Payment.py): explicit approval/rejection helpers, which affect
  workers and requester payments.
- [Image layout](imm/layoutImages.html) and
  [video layout](vid/layoutVideo.html): worker-facing templates.
- [Related PHP dashboard](https://github.com/edoardorex/DashboardForMTurkResults):
  visualizations of the collected database.

Respect participant consent, identifier privacy, and media rights.

## Original demonstration

[![Watch the original workflow](InkedCattura_LI.jpg)](https://drive.google.com/file/d/1C38FKbey1eFeFJC5GUU5YzXqckEMmoSj/view?usp=sharing)

## Attribution

The original README credits **Edoardo Re, 2019**.
This is a fork of
[edoardore/ImageAndVideoQualityAmazonMTurk](https://github.com/edoardore/ImageAndVideoQualityAmazonMTurk).
Original source and history are preserved; no software license is added or changed.
