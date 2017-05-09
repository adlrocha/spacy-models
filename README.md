# Resources and notes for custom SpaCy model generations

v.0.1. Temporary notes. Further explanations and sample model generation will be provided.

-----------------------------
Create vocabulary Wikipedia
-----------------------------
Download repository:
https://github.com/explosion/spacy-dev-resources/tree/master/vocab

Install dependencies

Run fab -f vocab/fabfile install_dep which will install the necessary python packages, downloads and install the Brown clustering script.
Usage

To create vocabulary files from Wikipeda run the following command from the root directory

fab -f vocab/fabfile build_wiki_vocab:<lang>

In case you already have a corpus, you can also build a vocabulary using your data by running

fab -f vocab/fabfile build_vocab:<lang>,<corpus_files_root> (where corpus_files_root directory contains plain text files)
Putting this all together

Your vocabulary is placed at ./data/model/<lang>/vocab, now you can build your language model using spaCy's CLI

First, create a spaCy model:

python -m spacy model <lang> ./data/model/<lang>/spacy_model ./data/model/<lang>/<lang>_wiki.freqs ./data/model/<lang>/brown/paths ./data/model/<lang>/<lang>_wiki.word2vec.bz2

Then, package it:

mkdir -p ./data/model/<lang>/package && python -m spacy package ./data/model/<lang>/spacy_model ./data/model/<lang>/package

cd ./data/model/<lang>/package/<package_name> && python setup.py sdist


-------------------------------
Create parser and POS tagger
-------------------------------
Download ancora corpus fron UD_DEPENDENCY Treebank
https://github.com/ryanmcd/uni-dep-tb
https://github.com/UniversalDependencies/UD_Spanish

Convert the .conllu files for -train and -dev downloaded from the ancora corpus
python -m spacy convert [input_file] [output_dir] [--n_sents] [--morphology]
python -m spacy convert es-universal-train.conllu ./

Train tagger and parser 
python -m spacy train es ./model es-universal-train.json es-universal-dev.json
python -m spacy train [lang] [output_dir] [train_data] [dev_data] [--n_iter] [--parser_L1] [--no_tagger] [--no_parser] [--no_ner]

Package the model for its installation and distribution. Output tar.gz
python -m spacy package [input_dir] [output_dir] [--meta] [--force]

Install the package.
pip install /Users/you/en_core_web_md-1.2.0.tar.gz
python -m spacy link [package name or path] [shortcut] [--force]

Link the model so it can be called from SpaCy.
python -m spacy link en_core_web_md en_default
python -m spacy link /Users/you/model my_amazing_model
