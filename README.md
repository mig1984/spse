# spse

## install

1. apt install ruby [rubygems]
2. gem install mechanize
3. edit spse.cfg

## usage

spse export TRIDA PREDMET >> data.csv

spse import [doit] < data.csv

spse sync [doit] < data.csv

Without the 'doit' it means dry-run.

The difference between import and sync is that sync deletes scores which are not in the csv.
(Import does not delete anything, it just creates or updates).
