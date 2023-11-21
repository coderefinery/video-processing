# Video processing files

This repo has the files used to do our video processing.  It uses
[git-annex](https://git-annex.branchable.com/) for the big files and
other things are committed to git.  **It provides non-YouTube public
access to our videos**, and is also our working place for releasing
videos (so a lot of the instructions below are for those that help
processing them).

We also made [a description of git-annex for data management, targeted
to scientists and
researchers](https://scicomp.aalto.fi/scicomp/git-annex/), if you want
to know what's going on behind the scenes.



## What is available here?

Browse the repo - course links are below.  More can be added later
depending on demand.

* [Python for SciComp
  2022](https://scicomp.aalto.fi/training/scip/python-for-scicomp-2022/)
* [Python for SciComp
  2023](https://scicomp.aalto.fi/training/scip/python-for-scicomp-2023/)



## Getting public copies of videos from git-annex

Raw video data is stored using git-annex and synced around different
places (our HPC cluster, the computers that process the videos, the
object store Allas provided by CSC).  Allas allows you to download the
videos you might like:

```console
$ git clone https://github.com/coderefinery/video-processing/
$ git annex init
$ git annex enableremote allas
$ git annex get python-for-scicomp-2023/out/day1.1-icebreaker.mkv
get python-for-scicomp-2023/out/day1.1-icebreaker.mkv (from allas...)
```

Only processed videos are available to the general public (the raw
private ones are recorded with git-annex in this repo, but not
available for download).  Also, **this is a test setup** and
everything may be subject to change or depreciation.

(How was this set up?  Get the environment variables needed for the
[git-annex S3 special
remote](https://git-annex.branchable.com/special_remotes/S3/) - I did
this by running `allas_conf` on one of the CSC computers.  Then run
`git annex initremote allas type=S3 encryption=none chunk=50MiB
embedcreds=no host=a3s.fi protocol=https bucket=aaltoscicomp-video
publicurl=https://aaltoscicomp-video.a3s.fi/ fileprefix=1-
public=yes` - it caches the authentication locally on that computer
only, it doesn't spread to anywhere else.)



## How it works

This repository stores the stuff used to process videos for
CodeRefinery / Aalto Scientific Computing / etc(?).  Here's how it
works in general:

- On the streaming computer, videos are placed into `COURSE/raw/*.mkv`
- git-annex is used to sync everything around.
- Raw videos are copied to the cluster
- Whisper is used to generate a subtitle file `.srt`
- [ffmpeg-editlist](https://github.com/coderefinery/ffmpeg-editlist)
  is used to cut videos into segments and generate the YouTube
  descriptions.
- The subtitles can be fixed up at the same time as the video is being
  sliced.
- ffmpeg-editlist runs and splits the video into pieces.  This *also*
  splits the raw subtitle `.srt` files into subtitles of each
  sub-part.  This allows us to parellelize the subtitle fixing and the
  video slicing.
- ffmpeg-editlist also generates the descriptions for YouTube.
- A Makefile automates subtitle generation
- `git annex sync --content` moves all content around as desired,
  making sure that the cluster has a full copy and other remotes have
  only what they have requested.



## Subtitle editing

If you are helping with subtitle editing:

- Find the `COURSE/raw/*.srt` subtitle file and edit it as follows:

- I don't watch all the video, but (very quickly) browse the text.
  Think 5 minutes (or less) skimming per hour of video, if there are
  no changes.  Only focus on the important parts that can affect
  understanding, not making it a perfect presentation quality
  transcript.  (I don't watch the video, I assume the transcript is
  correct except when it's clearly written wrong.)

- Remove all names, replace with `[name]`.  Find and replace can be
  useful here, but note there may be misspellings too, so you may have
  to go try several times as you see more other spellings.

- Fix up any command names, for example `dash dash argument` becomes
  `--argument`, capitalization, etc.  And other things that affect
  understanding.

- If you can't understand what someone is trying to say, replace with
  `[???]` or similar.

- But it doesn't have to be perfect.  Getting it done fast is the most
  important thing.  "normal" speech doesn't have to be made perfect,
  but do what makes sense (what is worth your time).

- Various subtitle editor programs can make this easier, but it's also
  just a text file.  I've used `subtitleditor` on Linux, which can
  playback the video right at each subtitle if you need to hear the
  original.

- If you notice something very wrong (Whisper has broken, it's not
  adding punctuation, etc), then don't try to fix it up, just leave
  it and make it minally usable.



## Slicing the videos

If you are volunteering to help generate the edit list:

- See https://github.com/coderefinery/ffmpeg-editlist  for the basics.
- I guess in reality, look at a template file from a previous workshop
  and copy and modify that.
- Set all the things you see in that file.
- Priorities:
  - Roughly split the videos into the different lessons.
  - Try to remove the breaks and exercise times.  It's OK to have a
    few seconds before/after: better that than missing some.
  - When it's easy, add some chapters into it, so that people can
    quickly navigate around the video.
  - Make sure the description is accurate.
- (see other docs)



## git-annex setup for private video files

Raw videos files are private and only synced via our cluster.

Only do this if you are pulling the *private* (raw) big video files to
your own computer to view them: otherwise, you can use git normally
and the video files appear as broken symbolic links.  For the final
videos, you can get them using the public copy above.

Privacy notice: the git-annex info on which computers have which files get
publicly distributed through the repository (including through
Github).  The info about your computer is the UUID and the
`MY-COMPUTER-NAME` which is in the repo.

To set up this repo to connect to the Triton cluster:
```
(pull repo from github)
git remote add triton triton.aalto.fi:/scratch/scicomp/video-processing/
git config remote.triton.annex-shell /share/apps/git-annex/10.20230228.path/git-annex-shell
git annex init MY-COMPUTER-NAME  # set up git-annex
git annex wanted . present       # don't download everything, but keep what is here
git annex sync
git annex get python-for-scicomp/2023/raw/FILE.mkv
```
