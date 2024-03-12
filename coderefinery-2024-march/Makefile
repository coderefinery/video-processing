# Setup
#
# git clone triton:/scratch/scicomp/video/linux-shell-2023
# cd linux-shell-2023
# git config remote.origin.annex-shell /share/apps/git-annex/10.20230228.path/git-annex-shell
# git annex init MY-COMPUTER-NAME
# git annex sync --content



videos_mkv=$(wildcard out/*.mkv raw/day*.mkv)
videos_mp4=$(wildcard raw/day*.mp4)
aac=$(patsubst %.mkv, %.mkv.aac, $(videos_mkv)) $(patsubst %.mp4, %.mp4.aac, $(videos_mp4))
aac_wild=$(wildcard raw/*.mkv.aac out/*.mkv.aac raw/*.mp4.aac)
#srt=$(patsubst %.mkv.aac, %.mkv.srt, $(filter %.mkv.aac, $(aac_wild))) $(patsubst %.mp4.aac, %.mp4.srt, $(filter %.mp4.aac, $(aac_wild)))
srt=$(patsubst %.mkv, %.srt, $(videos_mkv)) $(patsubst %.mp4, %.mp4, $(videos_mp4))

#WHISPER=whisper --model large
SRUN=srun --pty --time=0-4 -c 4 --mem=10G -p dgx-common --gpus=1
WHISPER=singularity_wrapper run --model_directory ${large_v2} --device cuda

print:
	@echo videos_mkv: ${videos_mkv}
	@echo videos_mp4: ${videos_mp4}
	@echo aac: ${aac}
	@echo aac_wild: ${aac_wild}
	@echo srt: ${srt}

#ifneq ($(MAKECMDGOALS),srt)
#%.mkv.aac: %.mkv
#	ffmpeg -i $< -vn -acodec copy $@
#%.mp4.aac: %.mp4
#	ffmpeg -i $< -vn -acodec copy $@
#endif


%.srt: %.mkv
	$(SRUN) $(WHISPER) --output_format srt --initial_prompt="Welcome to CodeRefinery day four." --lang en --condition_on_previous_text False $< --output_dir $(dir $<)
%..srt: %.mp4
	$(SRUN) $(WHISPER) --output_format srt --initial_prompt="Welcome to CodeRefinery day four." --lang en --condition_on_previous_text False $< --output_dir $(dir $<)


#aac: $(aac)
srt: $(srt)

setup:
	git config remote.triton.annex-shell /share/apps/git-annex/10.20230228.path/git-annex-shell
	git annex wanted . anything
	git annex wanted triton 'include=*.aac or include=*.srt or present'

	echo '*' > .gitignore
	echo '!*.mkv' >> .gitignore
	echo '!*.mp4' >> .gitignore
	echo '!*.aac' >> .gitignore
	echo '!*.srt' >> .gitignore
	echo '!*.info.txt' >> .gitignore
	echo '!out' >> .gitignore


	echo '* annex.largefiles=nothing' > .gitattributes
	echo '*.mkv annex.largefiles=anything' >> .gitattributes
	echo '*.mp4 annex.largefiles=anything' >> .gitattributes
	echo '*.aac annex.largefiles=anything' >> .gitattributes
#	echo '*.srt annex.largefiles=anything' >> .gitattributes
#	echo '*.info.txt annex.largefiles=anything' >> .gitattributes

