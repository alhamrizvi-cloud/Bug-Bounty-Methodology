You need URLS.txt file for this or params.txt
### ▶ GREP for File / Folder Related Parameters

(**this is WHERE we identify possible file or directory inclusion**)
```bash
grep -Ei "file=|path=|page=|dir=|folder=|doc=|include=|inc=|template=|view=|load=|download=|read=|name=|resource=|img=|image=|pdf=" parameter.txt > lfi_candidates.txt
```
or u all can use gf patterns 
### ▶ Remove Duplicate URLs

```bash
sort -u lfi_candidates.txt > lfi_unique.txt
```
### ▶ using liffy

```bash
liffy -l lfi_candidates.txt -w lfipayloads.txt
```

### ▶ using fuff(best)
```
ffuf -u "http://www.dailymotion.com/?page=FUZZ&id=1539508" \
-w lfipayloads.txt \
-mc 200 \
-fs 0 \
-t 40
```
