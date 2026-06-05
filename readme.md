# pblog — ramblings.work

Hakyll source for [my blog](https://ramblings.work).

Pretty standard stuff, but free to use for anyone.

## Publishing

The site is hosted on **GitHub Pages** and built automatically by GitHub Actions
(`.github/workflows/pages.yml`): **just push to `master`** and it builds and
deploys to <https://ramblings.work> within a couple of minutes. No manual build
or server needed.

## Creating a new post

1. Add a file in `posts/` named `YYYY-MM-DD-some-slug.markdown`.
   The date controls ordering; the slug becomes the URL — e.g.
   `posts/2026-06-05-my-new-post.markdown` → `/posts/2026-06-05-my-new-post.html`.

2. Begin the file with this metadata block (the `---` fences are required),
   then write the body in Markdown:

   ```markdown
   ---
   title: My New Post
   tags: haskell, rust
   ---

   Your content here. Standard Markdown — `## headings`, links,
   `code`, fenced code blocks, etc.
   ```

   - `title` — shown on the page and in the post list (required).
   - `tags` — optional, comma-separated; each tag gets its own
     `/tags/<tag>.html` page.

3. **Images:** drop the file into `images/` and reference it with an absolute
   path, e.g. `![a balloon](/images/balloon.png)`. Commit the image too —
   anything not in git does not get deployed.

4. Commit and push:

   ```sh
   git add posts/2026-06-05-my-new-post.markdown
   git commit -m "New post: My New Post"
   git push
   ```

   The post then shows up on the home page and in the archive automatically.

## Local preview (optional)

Requires the Haskell toolchain (Stack):

```sh
stack build            # first time only: compiles the site generator
stack exec site watch  # serves http://localhost:8000 and live-rebuilds on save
```

CI builds with Stackage `lts-22.43` (GHC 9.6 / Hakyll 4.16), pinned in `stack.yaml`.
