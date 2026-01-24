Git: Ignore files that were already pushed

Accidentally pushed files/folders matching `h-*` to GitHub.  

1. Add to `.gitignore`:    
```
**/h-*
```


2. Untrack the already-pushed files (doesn’t delete your local files):
```bash
git rm -r --cached -- '**/h-*'
```


3. Commit + push:
```bash
git commit -m "Ignore h-* files" 
git push
```


## Notes / Gotchas
- `.gitignore` only prevents **new** tracking. It does not remove files already committed.
- This removes from the **latest repo state**, but files may still exist in **Git history**.
- If those files contain secrets (keys/tokens), you must rewrite history + rotate keys.