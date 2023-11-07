# Video processing files

This repo has the files used to do our video processing.  It uses
git-annex for the big files and other things are committed to git.

## Subtitle editing
- Remove all names, replace with `[name]`
- Fix up any command names, for example `dash dash argument` becomes
  `--argument`, capitalization, etc.
- But it doesn't have to be perfect.  Getting it done fast is the most
  important thing.  "normal" speech doesn't have to be made perfect,
  but do what makes sense.

## Slicing the videos
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
