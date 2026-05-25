---
name: karakeep-mcp-first
description: Unofficial MCP-first skill for using Karakeep with AI agents, with CLI and API fallback for unsupported operations, bulk work, debugging, and highlight creation.
metadata:
  tags: bookmarks, bookmark manager, mcp, ai agents, productivity
  openclaw:
    envVars:
      - name: KARAKEEP_API_KEY
        required: true
        description: The API key for your Karakeep instance.
      - name: KARAKEEP_SERVER_ADDR
        required: false
        description: The server address for your Karakeep instance.
    requires:
      env:
        - KARAKEEP_API_KEY
        - KARAKEEP_SERVER_ADDR
      bins:
        - karakeep
    homepage: https://karakeep.app
    links:
      repository: https://github.com/karakeep-app/karakeep
      documentation: https://docs.karakeep.app
      video: https://youtu.be/WQNhRyYyl64
    cliHelp: karakeep --help
    install:
      - kind: node
        package: "@karakeep/cli"
        bins: [karakeep]
---

# Karakeep MCP-First

Karakeep is an open source self-hosted bookmark manager for collecting, organizing, and searching content. Use this skill when the user wants an AI agent to interact with their Karakeep instance, including adding bookmarks, searching bookmarks, managing lists, assigning tags, cleaning up duplicates, creating useful highlights, or organizing saved material.

This is an unofficial MCP-first Karakeep skill. It is designed to prefer Karakeep's official MCP tools for normal agent work, while keeping the Karakeep CLI and API available as fallbacks when MCP does not expose the needed operation.

## Tool Priority

- Preferred: Karakeep MCP
- Fallback: Karakeep CLI
- Specific exception: Karakeep API for highlight creation until MCP supports it
- Last resort: manual/browser workaround

## Operating Rule

Use the official Karakeep MCP server first for normal operations. Do not mix MCP and CLI in the same workflow unless there is a clear reason. If you use the CLI fallback, briefly explain why MCP was not used. Highlight creation is the narrow exception: use MCP to retrieve bookmark/content, then use the Karakeep API to create highlights because MCP does not currently expose highlight creation.

Use MCP for:

- Searching bookmarks: `search-bookmarks`
- Getting bookmark details: `get-bookmark`
- Creating bookmarks: `create-bookmark`
- Updating bookmarks: `update-bookmark`
- Reading bookmark content: `get-bookmark-content`
- Getting lists: `get-lists`
- Creating lists: `create-list`
- Adding bookmarks to lists: `add-bookmark-to-list`
- Removing bookmarks from lists: `remove-bookmark-from-list`
- Attaching tags to bookmarks: `attach-tag-to-bookmark`
- Detaching tags from bookmarks: `detach-tag-from-bookmark`

Use the Karakeep CLI only when:

- The MCP server is unavailable.
- The needed action is not exposed by MCP.
- The task is bulk import/export, dump, migration, or wipe work.
- You are debugging connectivity, auth, or server behavior.
- You need raw JSON verification.

If MCP does not expose a complete tag-management workflow, use MCP for attaching/detaching known tags and use CLI/API fallback only for the unsupported part.

Use the Karakeep API only when:

- Creating highlights, because MCP does not currently expose highlight creation.
- A needed operation is unavailable in both MCP and CLI and the API supports it.

Keep API work narrow, authenticated, and non-noisy. Explain the API fallback reason.

## Organization Strategy

Keep Karakeep tidy and predictable:

- Deduplicate before creating: search by URL/title/topic before adding a new bookmark when duplicates would matter.
- Prefer consistent, lowercase tags such as `ai`, `research`, `reference`, `todo`, `read-later`, `dev`, `business`, `video`, or project-specific tags.
- Use lists for durable collections, projects, and workflows. Use tags for reusable cross-cutting labels.
- Use smart-list style search queries when a category can be expressed by tags, text, dates, or state.
- Categorize bookmarks with the minimum useful structure: a good title, relevant tags, and a list only when it helps retrieval.
- Clean up low-value or broken items when asked, but confirm before destructive deletes or broad archive operations.

## Core Concepts

### Bookmarks

- Bookmarks can be links, text, or media.
- Links save URLs; Karakeep fetches metadata, screenshots, and archived page content.
- Text bookmarks are useful for quick notes, research snippets, and lightweight todos.
- Media bookmarks store uploaded images or PDFs.
- Favorites mark important items for quick access.
- Archiving hides items from the homepage while keeping them searchable.
- Notes and highlights add personal context.

### Lists

- Manual lists are curated collections organized by project or topic.
- Smart lists are auto-updating collections powered by search queries, such as `#ai -is:archived`.
- Use lists when membership itself matters, such as a project reading list, source library, or publish queue.

### Tags

Tags are lightweight labels for any bookmark. Multiple tags can apply to one bookmark, and tags travel with bookmarks across lists. Karakeep can auto-generate AI tags when inference is configured.

When assigning tags:

- Reuse existing tag names when possible.
- Avoid near-duplicates such as `ai-tools`, `aitools`, and `ai tool`.
- Prefer singular/plural consistency within the user's existing library.
- Attach/detach tags through MCP when the tag is known.

## Search Query Language

Karakeep supports full-text search, boolean logic, qualifiers, and date/age filters.

### Basic Syntax

- Spaces between conditions act as implicit AND.
- Use `and` / `or` for explicit boolean logic.
- Prefix a qualifier with `-` or `!` to negate it, such as `-is:archived`.
- Use parentheses for grouping.
- Any text not part of a qualifier is treated as full-text search.

### Qualifiers

| Qualifier | Description | Example |
|-----------|-------------|---------|
| `is:fav` | Favorited bookmarks | `is:fav` |
| `is:archived` | Archived bookmarks | `-is:archived` |
| `is:tagged` | Bookmarks with one or more tags | `is:tagged` |
| `is:inlist` | Bookmarks in one or more lists | `is:inlist` |
| `is:link` | Link bookmarks | `is:link` |
| `is:text` | Text/note bookmarks | `is:text` |
| `is:media` | Media bookmarks | `is:media` |
| `is:broken` | Bookmarks with failed crawls or non-2xx status codes | `is:broken` |
| `url:<value>` | Match URL substring | `url:github.com` |
| `title:<value>` | Match title substring | `title:"my title"` |
| `#<tag>` or `tag:<tag>` | Match bookmarks with a tag | `#important`, `tag:"work in progress"` |
| `list:<name>` | Match bookmarks in a list | `list:reading`, `list:"to review"` |
| `after:<date>` | Created on or after date | `after:2024-01-01` |
| `before:<date>` | Created on or before date | `before:2024-12-31` |
| `age:<time-range>` | Filter by creation age | `age:<1d`, `age:>2w`, `age:<6m` |
| `feed:<name>` | Bookmarks imported from an RSS feed | `feed:Hackernews` |
| `source:<value>` | Match capture source | `source:rss`, `-source:web` |

### Examples

```text
# Favorited bookmarks from 2024 tagged "important"
is:fav after:2024-01-01 before:2024-12-31 #important

# Archived bookmarks in "reading" list or tagged "work"
is:archived and (list:reading or #work)

# Untagged or unorganized bookmarks
-is:tagged or -is:inlist

# Recent bookmarks from the last week
age:<1w

# Full-text search combined with qualifiers
machine learning is:fav -is:archived
```

## MCP Workflows

### Add a Bookmark

1. Search first if the URL/topic may already exist.
2. Create the bookmark with MCP.
3. Attach relevant known tags with MCP.
4. Add it to a list with MCP only if list membership is useful.
5. Do not verify with CLI unless there is a clear reason.

### Search and Retrieve

1. Use `search-bookmarks` with a precise query.
2. Use `get-bookmark` for metadata or `get-bookmark-content` when the saved page text matters.
3. Prefer one focused search over several broad noisy searches.

### Organize Existing Bookmarks

1. Search for candidates.
2. Deduplicate by URL/title/content.
3. Attach/detach tags with MCP.
4. Create lists with MCP when needed.
5. Add or remove bookmarks from lists with MCP.
6. Confirm before destructive cleanup.

## Meaningful Highlights

Do not create highlights by default. Highlights should be rare, specific, and useful for the user's reason for saving or reviewing the bookmark.

Create highlights only when:

- The user's request implies a specific part of the content to preserve, such as ingredients, pricing, steps, definitions, quotes, warnings, requirements, commands, conclusions, or compatibility details.
- The bookmark has one obvious key passage that is basically the reason to save it.

Do not create highlights when:

- The passage is merely generally interesting.
- The highlight would duplicate the title, summary, or tags.
- The exact text is not present in the bookmark content.
- You would need to highlight most of the article to make the bookmark useful.

Highlighting rules:

- Highlight the smallest useful passage, not the whole article.
- Prefer 1-3 highlights unless the user explicitly asks for more.
- Each highlight must include a short note explaining why it matters for the user's request.
- Notes should be concrete, such as `Ingredients requested by the user`, `Core compatibility requirement`, or `Main finding worth returning to`.
- Avoid vague notes such as `Important`, `Interesting`, or `Key point`.

### Highlight Workflow

1. Use MCP to create, search, or get the bookmark.
2. Use MCP `get-bookmark-content` to fetch the saved content.
3. Identify the exact text span worth highlighting.
4. Check for existing similar highlights when practical.
5. If highlighting is warranted, use the Karakeep API `POST /api/v1/highlights` because MCP does not currently expose highlight creation.
6. Include `bookmarkId`, `startOffset`, `endOffset`, `text`, `note`, and a conservative color such as `yellow`.
7. Explain that API fallback was used because highlight creation is not available through MCP.
8. Verify with API or CLI only when needed.

### Highlight Examples

- Recipe request: highlight the ingredients block; note `Ingredients requested by the user`.
- Research paper: highlight the main finding or methodology only if it is the core reason for saving.
- Product page: highlight price, specification, compatibility, or limitation when that was the user's intent.
- Tutorial: highlight commands or critical steps, not generic introduction paragraphs.
- Legal/policy page: highlight the exact requirement, deadline, exception, or restriction the user asked about.

## CLI Fallback

Use the CLI only for fallback/debug/bulk/raw-verification work. When using it, say why MCP was not used.

### Installation

```bash
npm install -g @karakeep/cli
```

### Authentication

The CLI requires an API key and server address. Prefer existing configured auth when available. Avoid printing API keys.

```bash
export KARAKEEP_API_KEY="your-api-key"
export KARAKEEP_SERVER_ADDR="https://your-karakeep.example.com"
```

or:

```bash
karakeep --api-key <key> --server-addr <addr> <command>
```

### Useful Fallback Commands

```bash
# Verify authentication or server state
karakeep whoami --json

# Raw JSON search verification
karakeep bookmarks search "is:fav #work" --json
karakeep bookmarks search "rust" --limit 10 --sort-order relevance --json

# Get details or content when MCP is unavailable
karakeep bookmarks get <bookmark-id> --json
karakeep bookmarks get <bookmark-id> --include-content --json

# Bulk or administrative flows
karakeep dump --help
karakeep migrate --help
karakeep wipe --help
```

Do not use CLI-first bookmark/list/tag examples for normal work when MCP is available. Use the MCP tools instead.

## Last Resort

Use browser automation, direct API calls, or manual web UI steps only when both MCP and CLI are insufficient or unavailable. Explain the limitation and keep the workaround narrow.
