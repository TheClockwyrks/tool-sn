# history

The record of how this repository's history was extracted from the monorepo,
[The Test Cabinet](https://github.com/TheClockwyrks/TheTestCabinet), where the
crates here lived until the extraction. Nothing builds or tests against these
files; they are kept so the provenance outlives the scratch clone the
extraction ran in.

## The extraction

The history below the scaffold commit is the monorepo's `issue/repos-4` at
`25923d22c5ec8eabf96f1b97f21a546c1ac3db25`, the branch whose commit
`77c83390e` moved the crates' model-core, voxel-mesh and model-skin
dependencies onto the tool-model-core repository, filtered to the paths in
[`paths.txt`](paths.txt) by git filter-repo 2.47.0:

```sh
git clone -q --no-local --single-branch --branch issue/repos-4 --no-tags \
  <the-test-cabinet> x
cd x
uvx --from git-filter-repo==2.47.0 git-filter-repo --force \
  --paths-from-file ../paths.txt --prune-empty always --preserve-commit-hashes
```

Of the 6,976 commits reachable from that commit, 21 touched a path in the list
and were kept (1 of them a merge); the rest were pruned. No tag was carried.
The tip tree of every moved directory hashes the same as the monorepo's at that
commit:

```text
crates/sn/       b70922e9a6683f4d654e74d1b8a146c4ec72d6a3
crates/sn-anim/  6fcf505c8db63828fcf17e8c4e67d6efde4b0faa
crates/sn-skin/  2da88b02c2410a9e8ae9c80a9d9e73e7d3222ab4
```

The paths are kept where they were in the monorepo, so the filter needed no
rename and the path dependency of `sn-anim` on `sn` works as it did.

## The files

- [`paths.txt`](paths.txt) is the list handed to `--paths-from-file`
  (sha256 `61c731097b85262426c9b2b201d7b080d018249a17621c1cabd04987636cbbc9`).
  It is generated, not hand-written: the three moved directories
  (`crates/sn/`, `crates/sn-anim/`, `crates/sn-skin/`), and every path a
  tracked file under them had before a rename or copy, collected by
  `git log --follow -M -C --name-status` over each of the 8 files. Two copies
  were found: `crates/sn/src/bin/sn.rs` began as a copy of
  `crates/voxel/src/bin/voxel.rs`, and `crates/sn-anim/src/main.rs` as a copy
  of `crates/voxel-anim/src/main.rs`, which was itself renamed from
  `crates/voxel/src/bin/voxel-anim.rs`. Those three paths are in the list.
- [`commit-map`](commit-map) is filter-repo's map from each monorepo commit (left)
  to the commit it became here (right), all zeros for a pruned one.
  `--preserve-commit-hashes` left the commit ids quoted in the messages as they
  were, so an id in a message below the scaffold commit names a monorepo commit,
  and this map translates it.

## The live-path review

`paths.txt` intersected with the monorepo's tree at the extraction commit names,
outside the three moved directories, exactly two live files:

```text
crates/voxel/src/bin/voxel.rs
crates/voxel-anim/src/main.rs
```

Each is a copy source: an sn file began as a copy of it, and the voxel crates
kept their own, which now live in the tool-voxel repository. They were kept in
the list so `git log --follow` and blame of the files copied from them reach
their history before the copy. Their commits after the copy, which touch the
voxel binaries only, are in this history too, and the scaffold commit deletes
the two files. The third path, `crates/voxel/src/bin/voxel-anim.rs`, was
already gone from the monorepo's tree.
