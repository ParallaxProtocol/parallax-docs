# Parallax Documentation

Technical documentation for the Parallax protocol, the Parallax client,
Hashwarp, and the guides around them. Published at
[docs.parallaxprotocol.org](https://docs.parallaxprotocol.org).

## Local development

Install the [Mintlify CLI](https://www.npmjs.com/package/mint). It refuses to
run on Node 25 or newer, so use an LTS release:

```
npm i -g mint
```

Preview locally:

```
mint dev
```

View at `http://localhost:3000`. Before pushing, run:

```
mint validate
mint broken-links --check-redirects
```

## Structure

Each top-level folder is one tab of the site and changes on its own schedule:

| Folder                  | Tab                   | Versioned |
| ----------------------- | --------------------- | --------- |
| `protocol/`             | The Parallax Protocol | No        |
| `client/`               | Parallax Client       | Yes: the current major (v2), unversioned paths |
| `client/v1.x/`          | Parallax Client       | Yes: archived, frozen |
| `hashwarp/`             | Hashwarp              | No        |
| `guides/`               | Guides                | No        |
| `exchange-integration/` | Exchange Integration  | No        |

Shared assets stay at the root: `index.mdx`, `logo/`, `images/`,
`favicon.svg`.

## Versions

Only the client documentation is versioned, and only **by major release**.
The current major lives at unversioned paths: `client/` covers every 2.x
release and is edited in place as new ones ship, so `/client/...` links
always show the current client and never need updating. Archived majors get a
folder of their own (`client/v1.x/`). `/client/v2.x/...` redirects to
`/client/...` for links that want to pin v2. Nothing else has versions:

- **Protocol** pages describe the network's rules. When a rule changes, the page
  says from which upgrade or block it applies, instead of forking the page.
- **Guides**, **Hashwarp** and **Exchange Integration** always describe the
  current software. Where a step depends on a client version, link to the client
  page that documents it.

### Marking what changed within a major

A feature added after `X.0.0` names the release that added it, so readers on
an older release of the same major know it isn't there yet:

```mdx
<Note>Tor support was added in v2.1.0.</Note>
```

or inline, for a single field or flag: "It was added in v2.1.2." A behaviour
change that is not an addition says what older releases do instead. Never
document unreleased behaviour as if it shipped: note the release it will ship
in once that is known, and merge the docs change with the release.

### A new major release

The current pages stay where they are, so no link to `/client/...` changes.
The outgoing major is archived into a new folder:

1. Snapshot the outgoing tree into `client/v2.x/`: copy every page of
   `client/` except the `v*.x/` folders, and rewrite links to current client
   pages inside the copy from `/client/` to `/client/v2.x/` (leave
   `/client/v1.x/` links alone).
2. In `docs.json`, add a `v2.x` entry with `tag: "Archived"` listing the copied
   pages under `client/v2.x/`, and rename the current entry to `v3.x`
   (keeping `default: true` and `tag: "Latest"`).
3. Remove the `/client/v2.x/<page>` → `/client/<page>` redirects, since those
   pages now exist, and add one `/client/v3.x/<page>` → `/client/<page>`
   redirect per current page.
4. Point the old-layout v2 redirects (`/v2.1.x/parallax-client/:slug*` and the
   other v2 prefixes) at `/client/v2.x/:slug*`, and make them permanent.
5. Then edit `client/` for v3. `client/v2.x/` is frozen: no edits, not even
   link fixes.

### A page path belongs to exactly one version

Mintlify works out which version the reader is on by matching the URL against
each version's page list. If two versions claim the same path, the picker falls
back to the default version and shows the wrong sidebar. So a version never
points at another version's pages; it has a folder of its own. Neither
`mint validate` nor `mint broken-links` catches this: check it in `mint dev` by
switching versions in the picker.

## Links inside pages

Use **absolute** links: `/client/fundamentals/cli`,
`/protocol/foundational-topics/blocks`. They survive a page moving between
folders. Link to `/client/...` for the current client; an archived major's
pages link only within their own folder (`/client/v1.x/...`). Omit the `.mdx` extension. Image paths are absolute
too: `/images/...`.

## Redirects

`docs.json` redirects every URL of the old layout, where each version folder
held all sections (`/v2.1.x/parallax-protocol/...`, `/v2.0.0/guides/...`), to
its page in the new layout. The v1 rules are permanent. The v2 rules
(`/v2.1.x/parallax-client/...` and other v2 prefixes) point at the current
client pages and are not permanent, because they move to `client/v2.x/` when v2
is archived; the same goes for the `/client/v2.x/...` aliases.

The aliases are one redirect per page, not a `/client/v2.x/:slug*` wildcard:
Mintlify ignores a wildcard redirect whose source sits under a folder that
holds pages, such as `/client/`. Wildcards work for prefixes with no pages of
their own, like `/v2.1.x/`.

When you rename or move a page, add a redirect from its old path.

## Publishing

Changes pushed to the default branch are deployed automatically via the
Mintlify GitHub integration.
