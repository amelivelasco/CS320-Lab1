
MAKEFLAGS += -j6

BUILD_DIR ?= ./build
OUT_DIR ?= ./out

# Ensure the build and output directories are absolute paths
REAL_BUILD_DIR := $(shell realpath $(BUILD_DIR))
REAL_OUT_DIR := $(shell realpath $(OUT_DIR))

STUDENTS ?= ./test_students.csv

SCIPERS = $(shell mlr --csv --from $(STUDENTS) --headerless-csv-output uniq -g Sciper)
STUDENT_PDFS = $(foreach sciper,$(SCIPERS),$(REAL_BUILD_DIR)/$(sciper).pdf)

TEXARGS ?= -interaction=batchmode -halt-on-error -pdf -pdflatex=lualatex -quiet

.PHONY: default makeup all clean

default:
	latexmk $(TEXARGS) main.tex

makeup:
	mkdir -p $(REAL_BUILD_DIR)
	mkdir -p $(REAL_OUT_DIR)
	@echo "Generating makeup draft PDFs"
	MAIN_PATH=$(shell pwd)/makeup.tex && \
	echo "\input{$$MAIN_PATH}" > $(REAL_BUILD_DIR)/makeup-draft.tex && \
	echo "\def\ANSWERS{1}\input{$$MAIN_PATH}" > $(REAL_BUILD_DIR)/makeup-draft-sol.tex && \
	latexmk $(TEXARGS) -jobname=makeup-draft -output-directory=$(REAL_BUILD_DIR) $(REAL_BUILD_DIR)/makeup-draft.tex && \
	latexmk $(TEXARGS) -jobname=makeup-draft-sol -output-directory=$(REAL_BUILD_DIR) $(REAL_BUILD_DIR)/makeup-draft-sol.tex && \
	cp $(REAL_BUILD_DIR)/makeup-draft.pdf $(REAL_OUT_DIR)/makeup-draft.pdf && \
	cp $(REAL_BUILD_DIR)/makeup-draft-sol.pdf $(REAL_OUT_DIR)/makeup-draft-sol.pdf

draft:
	mkdir -p $(REAL_BUILD_DIR)
	mkdir -p $(REAL_OUT_DIR)
	@echo "Generating draft PDFs"
	MAIN_PATH=$(shell pwd)/main.tex && \
	echo "\input{$$MAIN_PATH}" > $(REAL_BUILD_DIR)/draft.tex && \
	echo "\def\ANSWERS{1}\input{$$MAIN_PATH}" > $(REAL_BUILD_DIR)/draft-sol.tex && \
	latexmk $(TEXARGS) -jobname=draft -output-directory=$(REAL_BUILD_DIR) $(REAL_BUILD_DIR)/draft.tex && \
	latexmk $(TEXARGS) -jobname=draft-sol -output-directory=$(REAL_BUILD_DIR) $(REAL_BUILD_DIR)/draft-sol.tex && \
	cp $(REAL_BUILD_DIR)/draft.pdf $(REAL_OUT_DIR)/draft.pdf && \
	cp $(REAL_BUILD_DIR)/draft-sol.pdf $(REAL_OUT_DIR)/draft-sol.pdf

all: $(STUDENT_PDFS)
	@echo "Individual PDFs generated."
	@echo "Copying PDFs"
	@mkdir -p $(REAL_OUT_DIR)
	cp $(STUDENT_PDFS) $(REAL_OUT_DIR)

$(REAL_BUILD_DIR)/%.pdf: 
	@echo "Processing Sciper $*"
	@mkdir -p $(REAL_BUILD_DIR)
	STUDENT_DATA=$$(mlr --csv --from $(STUDENTS) --headerless-csv-output filter "\$$Sciper==\"$*\""); \
	echo "Student data: $$STUDENT_DATA"; \
	SCIPER=$$(cut -d, -f1 <<< $$STUDENT_DATA); \
	NAME=$$(cut -d, -f2 <<< $$STUDENT_DATA); \
	ROOM=$$(cut -d, -f3 <<< $$STUDENT_DATA); \
	SEAT=$$(cut -d, -f4 <<< $$STUDENT_DATA); \
	TIME=$$(cut -d, -f5 <<< $$STUDENT_DATA); \
	MAIN_PATH=$(shell pwd)/makeup.tex; \
	echo "\def\theSciper{$$SCIPER}\def\theName{$$NAME}\def\theRoom{$$ROOM}\def\theSeat{$$SEAT}\def\theTime{$$TIME}\input{$$MAIN_PATH}" > $(REAL_BUILD_DIR)/$*.tex
	latexmk $(TEXARGS) -jobname=$* -output-directory=$(REAL_BUILD_DIR) $(REAL_BUILD_DIR)/$*.tex

clean:
	@echo "Cleaning up..."
	@latexmk -CA
	@rm -rf $(REAL_BUILD_DIR)
	@rm -rf $(REAL_OUT_DIR)
	@echo "Done."
