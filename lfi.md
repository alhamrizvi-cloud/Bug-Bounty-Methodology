You need URLS.txt file for this or params.txt
### ▶ GREP for File / Folder Related Parameters

(**this is WHERE we identify possible file or directory inclusion**)
```bash
grep -Ei "file=|path=|page=|dir=|folder=|doc=|include=|inc=|template=|view=|load=|download=|read=|name=|resource=|img=|image=|pdf=" parameter.txt > lfi_candidates.txt
```
### ▶ Remove Duplicate URLs

```bash
sort -u lfi_candidates.txt > lfi_unique.txt
```
### ▶ Basic File Inclusion Test (Linux)

```bash
liffy -l lfi_candidates.txt -w lfipayloads.txt
```

