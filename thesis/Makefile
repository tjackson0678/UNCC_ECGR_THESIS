T=tjacks5-msthesis
.PHONY: clean force curr
.SUFFIXES: .md .tex
.md.tex:
	rm -f $@
	pandoc --top-level-division=chapter $< -o $@
	chmod -w $@

TEXSRC=abs.tex intro.tex back.tex design.tex results.tex conc.tex appendix.tex glossary.tex

#curr: $(T).pdf
#	evince $(T).pdf

$(T).pdf: $(T).tex $(TEXSRC)
	xelatex $(T)
	bibtex $(T)
	xelatex $(T)
	xelatex $(T)

force:
	touch $(T).tex
	make curr

intro.tex: intro.md
back.tex: back.md
design.tex: design.md
results.tex: results.md
glossary.tex: glossary.md

clean:
	rm -f $(TEXSRC) \
	  $(TEXSRC:.tex=.aux) \
	  $(T).aux $(T).bbl $(T).blg $(T).log $(T).out \
	  $(T).lof $(T).lol $(T).lot $(T).toc

realclean: clean
	rm -f $(T).pdf

