# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A single-page Chinese-language frontend for an "教育知识库" (Educational Knowledge Base) file import system. The entire UI lives in `import.html` — a self-contained HTML/CSS/JS file with no build step or dependencies.

## Architecture

The app is a two-page SPA controlled by CSS class toggling (`active`/`hidden`):

1. **Type Selection Page** (`#pageSelect`) — User picks one of four import categories: 课程介绍, 文档片段, 项目资料, 知识题库. The latter three are locked (`.locked` class) until "课程介绍" data exists on the backend.
2. **Upload Page** (`#pageUpload`) — Drag-and-drop or click-to-upload files (PDF, Markdown, .docx). Each upload creates a file card with real-time progress tracking via polling.

### Backend API

The frontend expects a backend at `http://127.0.0.1:8002` with these endpoints:

- `GET /check_course_intro` — Returns `{ has_data: bool }` to gate unlock of dependent import types
- `POST /upload` — Accepts `multipart/form-data` with `file` and `content_type`; returns `{ task_id: string }`
- `GET /status/{task_id}` — Returns pipeline status: `{ status, done_list: string[], running_list: string[], durations: {} }`

### Pipeline System

Processing pipelines vary by file type and are hardcoded in `PIPELINES`:
- `.docx` → 9 steps
- `.pdf` → 8 steps
- `.md` → 7 steps

Progress is polled every 1.5s, with incremental log entries added as steps complete.

## Development

Open `import.html` directly in a browser. No build tools, bundlers, or package managers are needed. The backend must be running on port 8002 for full functionality.

## Custom Commands

- `/review` — Defined in `.claude/commands/review.md` (currently empty)

## Key Conventions

- All UI text is in Chinese (zh-CN)
- CSS uses custom properties (`--bg`, `--accent`, etc.) for theming; dark space theme throughout
- Animations are CSS-only (keyframes for twinkling stars, meteors, card transitions)
- No framework — vanilla JS with direct DOM manipulation
