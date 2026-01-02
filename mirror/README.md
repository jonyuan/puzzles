A collection of puzzle solution notes. Each subdir is a puzzle.

## Puzzle Release Notes

We use `git-subrepo` for subtree management: mirroring each puzzle into the public puzzles repo on release. To mirror:

```bash
cd ../puzzles_public_mirror
git subrepo clone git@github.com:jonyuan/puzzles-private.git mirror
git push
```

If changes are made later:

```bash
cd ../puzzles_public_mirror
git subrepo pull mirror
```
