exiftool
manage image (.jpg) file attributes

```
exiftool -common *      # show common values for all files
exiftool -T -common *   # tabular format
exiftool -s -common *   # use real tag names (useful for building more commands)


# Move all files into 
exiftool '-Directory<DateTimeOriginal' -d %Y_%m_%d *.jpg

# Sometimes, there may be no DateTimeOriginal set in image,
# so have to use FileModifyDate instead.

exiftool '-Directory<FileModifyDate' -d %Y_%m_%d *.jpg


exiftool '-Directory<DateTimeOriginal' -d %Y_%m_%d *.jpg -execute '-Directory<FileModifyDate' -d %Y_%m_%d *.jpg


# Rename all images in current dir based on create date and time.
# Preserve original extension.
# If there are duplicates, a copy number will be added %-c.
exiftool '-FileName<DateTimeOriginal' -d %Y_%m_%d_%H:%M:%S%%-c.%%e .
exiftool '-FileName<CreateDate' -d %Y_%m_%d_%H:%M:%S%%-c.%%e .
exiftool '-FileName<FileModifyDate' -d %Y_%m_%d_%H:%M:%S%%-c.%%e .
```
