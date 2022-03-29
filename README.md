## About The Project

- The Complete Git Guide: Understand and Master Git and GitHub
- Complete Git and GitHub guide (23 HOURS) - Master all Git features: commits, branches, merging, rebasing and squashing
- Tutorial for hidash (testing)
- [Bogdan Stashchuk GitHub](https://github.com/bstashchuk)
- [Bogdan Stashchuk Website](stashchuk.com)

&nbsp;

## Notes

### Git vs GitHub

- Distributed version-control system vs Repository hosting service
- A distributed version control system (DVCS) brings a local copy of the complete repository to every team member’s computer, so they can commit, branch, and merge locally. The server doesn’t have to store a physical file for each branch — it just needs the differences between each commit.

&nbsp;

### How Git Works Under The Hood

- Git Object Types
  - <b>Blob:</b> Files of any type
  - <b>Tree:</b> Directories
  - <b>Commit:</b> Version
  - <b>Annotated Tag:
    - <b>A lightweight tag</b> is very much like a branch that doesn’t change — it’s just a pointer to a specific commit.
    - <b>Annotated tags</b>, however, are stored as full objects in the Git database.

&nbsp;

### git hash-object

- Git uses SHA1 hash algorithm.
  - You can see that SHA1 is used because file folder name + filename exactly 40 hex characters long (Base 16).
  - [hash-function-transition: Migrate Git from SHA-1 to a stronger hash function.](https://git-scm.com/docs/hash-function-transition/)
  - [How to assign a Git SHA1's to a file without Git?](https://stackoverflow.com/questions/552659/how-to-assign-a-git-sha1s-to-a-file-without-git/552725#552725)

```sh
echo Hello, Git | git hash-object --stdin
echo Hello, Git | git hash-object --stdin -w

echo Hello, Git | shasum    # 0a2b198f595e55060dec9f0e196c10de86f2ca1c
echo -n Hello, Git | shasum # c9d5d04925b93d2fb99c73ab2b5869bde7405ca4
```

- What is the difference between - and -- ?
  - Usually - is used with one-letter options
  - Whereas -- with word options

&nbsp;

- Folder name + file name = hash of the file
- Hashs are one way function
- Output will always be the same when hashing same input

&nbsp;

- How many files Git can store in the same repository?
  - SHA1 hash is 40 Hex characters. Each Hex character is represented with 4 binary characters. Each binary character is either 0 or 1. That's why length of the SHA1 hash in binary characters is 40\*4=160. That's why total qty of permutations is 2^160
  - Or in another way,
    - 40 Hex characters
    - 16 possible characters on each place
    - Qty of possible permutations 16^40 = 2^4^40=2^160

&nbsp;

- What is the chance of producing same hash for different files (hash collision)?
- Probability of the same speficic SHA1 hash
  - 1/2<sup>160</sup> \* 1/2<sup>160</sup> = 1/2<sup>320</sup> which is 4.68e-97
- Probability of any same SHA1 hash on any pair of N files.
  - 1 - ((2<sup>160</sup> - 1)! / ((2<sup>160</sup> - 1)! \* (2<sup>160 \* (N-1)</sup>)))
  - N=2 6.84e-49
  - N=3 2.05e-48

> Hash Collision Probability is very, very low and it will almost never happen in the repository. But if it happens, in some cases Git will not tell you about that but pointers to blobs will be incorrect (if for example two blobs have same SHA1 hash). In some cases Git will tell you that repository is corrupt (if for example tree object and blob object have same SHA1 hash).

- [How would Git handle a SHA-1 collision on a blob?](https://stackoverflow.com/questions/9392365/how-would-git-handle-a-sha-1-collision-on-a-blob/9392525)
- [Birthday Problem](https://en.wikipedia.org/wiki/Birthday_problem)

&nbsp;

### git cat-file

| flag |  git cat-file options  |
| :--: | :--------------------: |
|  -p  | Contents of the object |
|  -s  |   Size of the object   |
|  -t  |   Type of the object   |

```sh
git cat-file -flag b7aec520dec0a7516c18eb4c68b64ae1eb9b5a5e
```

- By removing some characters it would still giving me the same results up to four characters.
  - As long Git is able to locate an uniques object using characters you supply it will give you result. It means there is no need to enter full SHA1 hash.

&nbsp;

### Git Object

- Content + Object Type + Object Length = Hash
- Hello, Git | blob | 11 | \0 (Delimiter)
- Escaped character \0 is also counted as character.
- \0 is null

```sh
echo 'blob 11\0Hello, Git' | shasum # b7aec520dec0a7516c18eb4c68b64ae1eb9b5a5e  -
echo -e 'blob 11\0Hello, Git' # blob 11Hello, Git
```

- [echo flags](https://linuxcommand.org/lc3_man_pages/echoh.html)
- [Inline Data Format](https://git-scm.com/docs/git-fast-import#Documentation/git-fast-import.txt-Inlinedataformat)
  - 100664: A normal (not-executable) group-writeable file.

&nbsp;

### git mktree

- Hash of the new tree Git object

```sh
find .git/objects -type f
# .git/objects/.DS_Store
# .git/objects/b7/aec520dec0a7516c18eb4c68b64ae1eb9b5a5e
pico temp-tree.txt
100644 blob b7aec520dec0a7516c18eb4c68b64ae1eb9b5a5e	file1.txt
cat temp-tree.txt | git mktree
# 0c047ef0c36b4d539f2c4cfa1676f26dc60ad724
git cat-file -p 0c04
# 100644 blob b7aec520dec0a7516c18eb4c68b64ae1eb9b5a5e	file1.txt
```

&nbsp;

### Working directory, staging area (index) & Git repository

- Commit (Root directory) -> Tree -> Blobs
- Every file in Git may have one of four tracking statuses:
  - Untracked
  - Modified
  - Staged
  - Unmodified

```sh
git read-tree 0c04      # Git Repo
git ls-files -s         # Staging Area
git checkout-index -a   # Working Directory
```

- How many folders could be created for objects?
  - Each folder name is 2 characters
  - 1 character is 16 hex
  - 16 \* 16 = 256 folders

```sh
git cat-file -t bd63    # Commit Object
git rm --cached <file>  # Unstage File
```

&nbsp;

### Branch

- Branch is just <u>text reference</u> to the commit.
- Default branch is main.
- Multiple branches can exist in the same repository.
- Pointers for all branches are located in <b>.git/refs/heads folder</b>.
- Current branch tracks new commits.
- Branch pointer moves automatically after every new commit.
- Change branch <b>git checkout &lt;branch&gt;</b>

&nbsp;

### HEAD

- HEAD is <u>reference</u> to the currently checked-out branch or commit.
- HEAD is locally significant.
- Pointer is located in the <b>.git/HEAD</b> file.
- Deafult pointer is <b>ref: refs/heads/main</b>.
- Change reference to specific branch <b>git checkout &lt;branch&gt;</b>.
- Change reference to specific commit <b>git checkout &lt;sha1&gt;</b>.
- Ideally, HEAD is pointing at branch latest commit. (Refer to Detach HEAD state)

&nbsp;

- Git will not delete any of the previously createed files or folders in the Git repository immediately even after user has deleted files and make a commit. They are removed in some time by garbage collector.

&nbsp;
