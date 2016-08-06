---
layout: post
comments: true
title: "Host code via Bitbucket"
excerpt: ""
date: 2013-01-22 11:00:00
mathjax: true
---

Similar to [GitHub](https://github.com/), [Bitbucket](https://bitbucket.org/) makes it very easy to host your code. We all know that GitHub works great for open-source. However, you have to pay if you want to create private repositories. On the other hand, Bitbucket enables you to create unlimited free private repositories. This is an important reason why more and more people are using Bitbucket nowadays. In this post, I will show how to use Bitbucket to easily host your code (over [TortoiseHg](http://tortoisehg.bitbucket.org/) specifically).

<!-- add TOC here -->
<div id="genTocHere"></div>

## Setup Bitbucket over Internet
1. Login `Bitbucket > Repositories > Create repository`.
2. In the Create a new repository page:
   - **Name**: Fill in the repository name (`testRepo` as an example).
   - **Description**: I will always leave this empty because I prefer to use a `README.text` file instead, which supports [Python-Markdown](http://pypi.python.org/pypi/Markdown) and is much more powerful.
   - ...

## Setup TortoiseHg
Simple way is to use **SSH keys** and follow setup sets by this post: [Set up SSH for Mercurial](https://confluence.atlassian.com/display/BITBUCKET/Set+up+SSH+for+Mercurial). Key step is to add the following content to `mercurial.ini` (TortoiseHg’s global setting file):

```ini
[ui]
username = -name-you-want-to-show- <-email-@xxmail.com>
ssh = "C:\Program Files\TortoiseHg\TortoisePlink.exe" -ssh -2 -batch -C
```

Note:

1. Make sure `Pageant.exe` is running in background in order to make it work (`Add key > load >` input passphrase).
2. It's possible to make `Pageant.exe` automatically load keys on system startup, see [this post](http://blog.shvetsov.com/2010/03/making-pageant-automatically-load-keys.html). Key step is add a shortcut of `Pageant.exe` to PC's `Startup` folder and change its `Target` to be something like:

    ```ini
    "C:\Program Files\TortoiseHg\Pageant.exe" "-path-of-your-ssh-key-file-.ppk"
    ```

## Commit your code using TortoiseHg
1. `File > New Repository >` Change `Destination path` to where your code is `> Create`.
2. Click `Commit` button in toolbar. It will list all the modified (including edit/add/delete…) files in the left window. Note, click `Refresh current repository` button in toolbar when needed if you cannot see the new updated files. Check the files that we want to check in. And you can right click > `Ignore` to ignores files to check in.
3. Fill in some commit update information in the right window and then click `Commit`.
4. Click `Push outgoing changes to selected URL` button in toolbar.
5. In `Remote Repository >` Select `https` >` Add the URL of the repo `testRepo` > Click `save` button in just this left to remember this URL so that you have no need to type again when doing further commit this repo to this URL.

Note: all you need to do is to loop in step 2~5 for further commits.

## Sync between multiple users/PCs
1. **Get latest**: Before editing, always get current latest version by `Repository > Synchronize > Pull`.
2. **Update local to latest**:  Right click current local version > `Update` > Write the latest revision in `Update to > Update`.
3. **Edit source**.
4. **Push**: `Commit > Push outgoing changes to selected URL`.
 Done if no conflict found with server version (e.g. no one pushed new revisions during our editing). If do find conflict, it will shows `abort: push creates new remote head xxxxxxx!` in TortoiseHg.
5. **Resolve conflicts**:
   - Get latest: `Repository > Synchronize > Pull`.
   - Merge: Right click latest revision by others that is just pulled `> Merge with Local… > Next >` Click `resolved` in current Merge window to resolve conflicts > Click the file (do this one by one) that we need to resolve in `Unresolved conflicts` sub-window and click `Tool Resolve` (assume `KDiff3` has been installed, this will call KDiff3 to compare both versions side by side) > In the `Output` window, right click `?<Merge Conflick> > Select Line(s) from` the version that you want (or both versions) `> Save` and `Close` KDiff3 > `Close` the Resolve Conflicts window > Change Commit message if you need in the `Merge` window `> Commit Now > Finish`.
6. **Push**: Push outgoing changes to selected URL.

## Other remarks
### Repo grouping
Use *teams* to group repos (only way currently). Issues can also be import/export in `Settings->Import & export->…`

### Get some specific version
- If there is no corresponding repo in your PC:
	1. Clone the repo to PC (done in TortoiseHg).
	2. Open it by TortoiseHg. Then all its revisions will show in the right view.
	3. Select the revision you want to get > Right click > `Export > Archive` > Choose the `Destination path > Archive`.

        > Clone vs. Archive:
        > - Clone will copy everything including `.hg` folder (can be opened by TortoiseHg).
        > - Archive will copy everything except .hg folder (can NOT be opened by TortoiseHg).

- Otherwise:
	- Select current revision > Right click > `Update…` > Put the revision you want to get in the `Update to` box > `Update`.

### Link to most-recent version of given file[^1]
1. Get the file link of any version, e.g. https://bitbucket.org/herohuyongtao/files/src/f7b75d07772182019e80200a18e5f1130f75fc70/files/VC_call_MATLAB.cpp?at=default
2. Change string corresponding to version (`f7b75d07772182019e80200a18e5f1130f75fc70` in above example) to `tip` or `default` (or any other branch name) and delete the tail until to the file type, i.e. to be   https://bitbucket.org/herohuyongtao/files/src/tip/files/VC_call_MATLAB.cpp or https://bitbucket.org/herohuyongtao/files/src/default/files/VC_call_MATLAB.cpp.

    > The difference between `tip` and `default` is that using `tip` for the very latest commit, regardless of branch. Instead, `default` or any other branch would link to the latest version under that branch.

### Delete local commits
- If only need to delete last commit, `Repository > Rollback/Undo...`. This will keep changes in local.
- If want to delete all local commits[^2]<sup>,</sup>[^6] (this will also revert all changes):
	1. Enable `mq` extension by adding the following to `mercurial.ini` or enable it via `File > Settings > Extensions > mq`:

    	```ini
    	[extensions]
    	mq=
    	```
	2. Run `hg strip 'roots(outgoing())'` in console.
    > Note: it seems that this doesn't work for `graft` commits, in this case, just use `hg strip #changeset#`.

### Copy changes from other commits
This can be viewed as one kind of merging except that all files are updated to other commits. This is very useful when you want to update old `default` branch to one new `branch`.

This can be done in the following steps[^5]:
1. Set the current commit to which you want to be overwritten by other commits by right click \\(\rightarrow\\) `Update...` \\(\rightarrow\\) set the commit number.
2. Select the commit that you want to copy changes from \\(\rightarrow\\) right click \\(\rightarrow\\) `Graft to Local...`.

### Ignore given files/folders
- Ignore all its content under folder, simply add `-path-to-folder-/folderName/` to file `.hgignore`.
- Ignore all its content under folder except given files or given sub-folders, add similar conent to file `.hgignore` (note that, unlike folders, files should end with `$`):

    ```ini
    syntax: regexp
    -path-to-folder-/folderName/(?!exceptFile1.ext1$|exceptFile2.ext2$|exceptSubFolder1|exceptSubFolder2|)
    ```

### Sub-repos
Subrepositories allow you to have a standalone repository included within a parent repository[^3] ([hg example](https://bitbucket.org/xixs/public) / [git example](https://github.com/willyd/caffe-windows-dependencies)).

1. In the main repo, create file `.hgsub`.
2. Add sub-repo mapping info in file `.hgsub` with format: `local_subrepo_path = external_repo_path` (where `local_subrepo_path` is relative to the root of your main repo). Suppose we want to add repo hosted at `https://me@bitbucket.org/me/subrepo` to folder `-path-to-main-repo-path-/libs/subrepo`, the content of file `.hgsub` will be like:

    ```ini
    libs/subrepo = https://me@bitbucket.org/me/subrepo
    ```
3. Add and commit file `.hgsub` to the main repo. File structure identified by `local_subrepo_path` will be created. E.g. in above example, folder `libs/subrepo` will be created automatically.

> Note: You must revert anything done to the sub-repo when pushing changes to the main repo, though you can modify it to fit your needs during development.

Note that, when cloning the main repo, by default, all sub-repos (and their contents) are also cloned. If you only want to download some of the sub-repos (especially useful if the sub-repos are huge in terms of number and size), follow the following steps:

1. Download the main repo from its download page.
2. In TortoiseHg \\(\rightarrow\\) `File` \\(\rightarrow\\) `New Repository...` \\(\rightarrow\\) browse to the download main folder \\(\rightarrow\\) `Create`.
3. Check on all files to commit (may need to press commit button twice).
4. Download sub-repos.
    - One-by-one:
        - In `Repository Registry` window \\(\rightarrow\\) click the main repo to expand all its sub-repos \\(\rightarrow\\) double click the sub-repo needed to download \\(\rightarrow\\) click `Pull` button.
    - Batch style:
        0. Download file [`onsub.py`](https://www.dropbox.com/s/zsx5av6mjeux47b/onsub.py) and put anywhere on your PC.
        1. In TortoiseHg \\(\rightarrow\\) right click the main repo \\(\rightarrow\\) on the main repo's repository setting window \\(\rightarrow\\) click `Edit File` \\(\rightarrow\\) add the following content to the opened file (i.e. `hgrc` file) and `Save`.

            ```ini
            [extensions]
            onsub = -path-to-onsub.py-/onsub.py
            ```

        2. Edit both files `.hgsub` and `.hgsubstate` to keep only the lines for the sub-repos that you want to download.
        3. Run `hg onsub "hg pull -u"` in console[^4].
        4. *(optional)* If you want to download given commit of one sub-repo, after the above steps, go to the sub-repo that you want to edit and update to the commit you want.

[^1]: Link to most-recent version of static file: https://bitbucket.org/site/master/issue/3769/link-to-most-recent-version-of-static-file.
[^2]: Delete all local changesets and revert to tree: http://stackoverflow.com/a/2143711/2589776.
[^3]: Getting Started With Mercurial Subrepositories: https://tomtech999.wordpress.com/2011/12/17/getting-started-with-mercurial-subrepositories/.
[^4]: OnsubExtension: https://www.mercurial-scm.org/wiki/OnsubExtension.
[^5]: Copy changes from other branches onto the current branch: https://selenic.com/hg/help/graft.
[^6]: Is there any way to delete local commits in Mercurial?: http://stackoverflow.com/questions/2338986/is-there-any-way-to-delete-local-commits-in-mercurial
