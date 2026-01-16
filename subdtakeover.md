## Subdomain Takeover

```bash
subzy run --targets bmw_resolved.txt --hide-fails
subzy run --targets bmw_resolved.txt --verify_ssl --concurrency 20
subzy run --targets bmw_resolved.txt | tee subzy_results.txt
```

## with nuclei

```bash
nuclei -l dailylive.txt -tags takeover -severity high,critical
```

```bash
nuclei -l dailylive.txt -tags takeover -severity high,critical -stats
```
