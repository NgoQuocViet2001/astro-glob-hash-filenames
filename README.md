# glob() loader skips content files with `#` in their names

Minimal reproduction for `astro@7.3.5`.

The `blog` collection in `src/content.config.ts` uses
`glob({ pattern: '**/*.md', base: './src/content/blog' })` and contains two files:

- `src/content/blog/hello.md`
- `src/content/blog/c#-basics.md`

`src/pages/index.astro` lists every entry of `getCollection('blog')` as `id: title`.

## Steps

```sh
npm install
npx astro build
```

Then open `dist/index.html`.

## Observed

The build logs an error for `c#-basics.md`, with a path that stops at the `#`,
and still finishes with `Complete!`:

```
[content] Syncing content
[ERROR] [glob-loader] Error reading c#-basics.md: ENOENT: no such file or directory, open '<project>\src\content\blog\c'
[WARN] [glob-loader] No contents found for c#-basics.md
[content] Synced content
...
[build] Complete!
```

`dist/index.html` only lists one entry:

```html
<ul><li>hello: Hello</li></ul>
```

`npx astro dev` logs the same error, and `http://localhost:4321/` also lists only `hello: Hello`.

## Expected

Both files are loaded and the page lists:

```html
<ul><li>c-basics: C# basics</li><li>hello: Hello</li></ul>
```
