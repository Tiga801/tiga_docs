# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Personal knowledge management repository combining:
- **Computer Vision documentation** (`computer_vision/`) - Task logic specs in Chinese for face detection, fall detection, and safety vest detection
- **AI Agent Skills & Plugins** (`marketplaces/`) - Curated collection of skill/plugin marketplaces as git submodules
- **Claude Code customizations** (`claude/`) - Custom commands and plugin configurations

## Repository Structure

- `computer_vision/` - CV task specifications with processing pipelines, judgment logic, and parameterizable thresholds
  - `face/` - Face detection with quality scoring
  - `violation/` - Safety violation detection (falls, missing safety vests) with voting/locking state machines
- `claude/` - Claude Code commands and plugin configurations
- `marketplaces/` - Git submodules containing skill/plugin marketplaces:
  - `anthropic-agent-skills/` - Anthropic's official skills (document creation, design, development)
  - `claude-code-workflows/` - 72 focused plugins with agents, skills, and tools
  - `planning-with-files/` - Manus-style context engineering skill
  - `superpowers-marketplace/` - 67 specialized agent plugins

## Key Conventions

- Computer vision docs are written in Chinese with detailed flowcharts and state transition logic
- Skills follow the Agent Skills spec: each skill is a folder with a `SKILL.md` containing YAML frontmatter (`name`, `description`) and markdown instructions
- Marketplaces are managed as git submodules - do not modify files inside `marketplaces/` directly

## Language

- General communication: Chinese preferred
- CV documentation files: Chinese, UTF-8 encoded
- Claude Code config files (CLAUDE.md, etc.): English
