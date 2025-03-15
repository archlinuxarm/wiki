This wiki is designed as a simple collection of markdown-formatted articles, providing information strictly related to Arch Linux ARM and its supported platforms.

All of the guidelines discussed here must be adhered to in all contributions. Failure to comply will result in a rejected submission.

## Git
This wiki is available [on GitHub](https://github.com/archlinuxarm/wiki). To contribute: clone the repository, make your modifications, and submit a pull request. See these pages for help:

* [Creating a pull request](https://help.github.com/articles/creating-a-pull-request/)
* [Working with forks](https://help.github.com/articles/working-with-forks/)
* [Changing a commit message](https://help.github.com/articles/changing-a-commit-message/)

Rules for pull requests:

1. **One pull request per topic.** If you want to address multiple topics on this wiki, create separate pull requests for each.
2. **Squash commits.** Your commits should be meaningful. If you make incremental changes or fixes, they must [be squashed](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History#Squashing-Commits) before the pull request will be merged.
3. **Annotate changes.** The pull request description as well as the commits must clearly summarize the changes being made.
4. **Supported platforms only.** Information on unsupported platforms may be discussed on the forum.
5. **Accurate information.** Inaccuracies, guesses, opinions, or any other non-factual or dangerous information is not permitted.

## Style Requirements

* Page file names must be named for the title of the page using only the permitted ASCII characters, with the file extension .mdx, and using proper capitalization.
    * Permitted characters: A-Z, a-z, 0-9, underscore, hyphen
* Spaces must be converted to underscores in the filename.
* Page names, including the top header, are derived from the filename, and are rendered using the &lt;h1&gt; tag. Therefore, headers within the file must be second level (##) or lower.
* Links to other wiki pages can be made by enclosing the page name (with proper capitalization) in double brackets, such as &#91;&#91;Contributing&#93;&#93;.
