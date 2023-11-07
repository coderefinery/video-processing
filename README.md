# Video processing files

This repo has the files used to do our video processing.  It uses
git-annex for the big files and other things are committed to git.



## Subtitle editing

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
- Edit the .yaml file.  See https://github.com/coderefinery/ffmpeg-editlist .
- (see other docs)



## git-annex setup

Only do this if you are pulling the big video files: otherwise, you
can use git normally.

Privacy notice: the git-annex info on which computers have which files get
publicly distributed through the repository (including through
Github).  The info about your computer is the UUID and the
`MY-COMPUTER-NAME` which is in the repo.

To set up your computer:
```
git remote add triton triton.aalto.fi:/scratch/scicomp/video/linux-shell-2023
git config remote.triton.annex-shell /share/apps/git-annex/10.20230228.path/git-annex-shell
git annex init MY-COMPUTER-NAME
git annex sync --content
git annex get python-for-scicomp/2023/raw/FILE.mkv
```
