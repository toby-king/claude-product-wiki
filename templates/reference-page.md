# Reference > [Topic] Page Template

Generic template for reference content like API endpoints, configuration options, or schema details. Use only when the content is genuinely needed — many smaller products won't need any reference pages.

## When to create reference pages

Create a Reference page when:
- There's a substantive list of API endpoints (10+) that consumers need to know about
- There are configuration options that materially affect product behaviour
- There's a schema or data format that callers need to conform to

Don't create reference pages for:
- Internal-only APIs that only the product itself calls (the code is the reference)
- Trivial configuration (a couple of env vars belong on the Env Vars page)
- Content that only one person ever looks at (just use code comments)

## Properties

- **Name**: `{Topic}` (e.g. "API Endpoints", "Webhook Reference", "Schema Reference")
- **Owner**: User
- **Verification**: Empty (draft)
- **Tags**: `Technical`, `Reference`

## Icon and cover

- **Icon**: 📚
- **Cover**: reference / documentation-themed gallery image

## Body structure

### 1. Definition callout

```
ℹ️ {What this reference covers and who uses it.}
```

### 2. Source of truth

```
> Source of truth: {file path or URL}. This page is a summary; the code/spec is canonical.
```

### 3. Reference content

The actual reference. Format depends on type:

**For API endpoints**: a table or list, one entry per endpoint.

```
### POST /api/applicants

**Description**: Create a new applicant for a job.
**Auth**: Bearer token (tenant-scoped)
**Request body**:
{
  "job_id": "uuid",
  "email": "string",
  "cv_url": "string"
}
**Response**: 201 with the created applicant object
**Errors**: 400 (validation), 401 (auth), 404 (job not found)
```

For longer endpoint lists, group by resource (Applicants / Jobs / Pipelines / etc.) with a heading per group.

**For configuration options**: a table.

| Key | Type | Default | Description |
|---|---|---|---|

**For schema reference**: similar to Data Model page but more exhaustive — every field with its type and constraints.

### 4. Related pages

Links to:
- Code that implements this (file paths)
- Workflow pages that use this reference
- Integration pages if this is for external consumers

## What NOT to include

- Generated content that will go stale (auto-generate from code if you need exhaustive reference)
- Examples copied from vendor docs
- Tutorial content (that belongs in workflow pages)

## Source notes

- Almost entirely code-verified — these pages are just human-readable summaries of what's in the code
- Should be regenerated from code on `update` rather than manually edited
