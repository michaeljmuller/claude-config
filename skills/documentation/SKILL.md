---
name: documentation
description: |
  Voice, structure and upkeep rules for project documentation: READMEs, the
  docs/ folder, setup and install guides, runbooks, design notes, release
  procedures and requests written for other people (admins, reviewers).
  Use whenever writing, rewriting, reorganizing or reviewing documentation,
  and when a code change makes existing documentation wrong. Sentence-level
  plain-English rules live in the simple-english skill; this skill decides
  who the text addresses, where it goes, and how it is laid out, and wins
  where the two disagree.
metadata:
  version: "0.4.3"
---

# Documentation

Rules for documentation in any project. Before writing or rewriting a
document, load the simple-english skill and apply its rules for documents
to the prose: sentence length, tenses, modals, one word for one meaning,
and so on. This skill covers what simple-english does not: who the text
addresses, which document content belongs in, and how documents are laid
out and kept up to date.

Where the two disagree, this skill wins. The known conflict: simple-english
names the actor with "you" ("You run the migration."); rule 1 below names
the role instead ("The operator runs the migration.").


## 1. Do not cast the reader in a role

People read documentation for many reasons: to do a task, to approve it, to
review it, to learn how a system works, or to look up one fact. Writing "you"
assigns every reader the role of the person doing the work, and "your"
assigns them ownership they may not have. Name the role instead.

- Describe the software in the third person: "GChat polls every 3 seconds."
  Not "Your messages are checked every 3 seconds."
- Name the role that performs an action: "A Workspace admin changes the
  setting.", "The person signing in chooses the account.", "The developer
  runs the release script." Not "You change the setting."
- Name the owner instead of "your": "the organization's Cloud project", "the
  login Keychain on the Mac". Not "your Cloud project", "your Keychain".
- Procedures meant to be followed step by step use the imperative mood:
  "Click Save.", "Run scripts/release.sh." A procedure is explicitly for
  whoever performs it, so the imperative does not assign a role. Introduce
  the procedure with the role it is for: "To renew the certificate, the
  account holder does the following:".
- No "we", "our" or "us" either. The text describes the system and its
  procedures, not a relationship between writer and reader.
- Chat replies to the user are not documentation. Address the user directly
  there.

Readers in a role still recognize themselves when the role is named, and
everyone else reads a description of the process.


## 2. Keep the README high level

The README answers: what is this, and is there anything critical to know
before going further. Usually that is:

- one short paragraph on what the project does and does not do;
- requirements or constraints that rule readers in or out (supported
  platforms, accounts, licences);
- a list of the documents in docs/, one line each saying what each covers.

Everything else goes in docs/: installation, usage, setup of external
services, design and implementation notes, building, releasing, signing,
troubleshooting, planned work. A detail that is not critical for every
reader does not belong in the README.


## 3. The document set

Every project gets the same set, so the question of what to write and where
is settled once. Each document has one job; content that belongs to another
document is linked, not repeated. Leave out a document a project has nothing
for (a project with no server has no web deployment).

- README: what the project is, what rules a reader in or out, and an index
  of docs/ (rule 2).
- requirements: what the person asked for, in their own terms, kept
  separate from behavior and api-contract. Every feature in those documents
  traces back to a requirement; one that traces back to none stands out and
  is either confirmed with the person or removed. Features come from requirements,
  not from whoever writes the code.
- behavior: what the software does, from the side of the people who use it:
  what happens when they click, type, run or wait, with defaults and limits.
  Not why. Present when people use the software directly; a project used
  only by other programs has api-contract in its place.
- api-contract: what other programs can rely on, in exact reference style,
  including what stays stable between versions. Present when the project
  exposes an interface to other programs:
  - a web service: endpoints, request and response formats, errors,
    authentication. This applies even when the only client is the project's
    own app, because the two ship separately and old clients stay in use;
  - a library: public functions and types, their arguments, return values
    and errors;
  - a command-line tool that scripts call: commands, options, output and
    exit codes;
  - a file or data format that other tools read.
  For a project whose main job is to serve other programs (an API service,
  a library), this is the primary document, and it usually replaces
  behavior: what the project does is what its interface promises. Keep a
  behavior document as well only if the project also has people who use it
  directly, for example through a web page or a command line.
  The test is a boundary between programs, not who owns them. An app made
  of components that talk to each other, such as a phone app and its
  server or a web front end and its backend, documents the interface
  between them here. A single program with no such boundary, such as a
  desktop app that only calls someone else's API, has no api-contract. Its
  use of that outside API is a design choice and belongs in design.
- design: how it is built and why, including options considered and
  rejected.
- prerequisites: everything needed before anything can be built, run or
  deployed: tools on the development machine and on the server, accounts,
  keys and external services. Collected in one place, not scattered through
  other documents. A tool that every build needs is listed here, not
  mentioned only as a build step where it reads as optional.
- development: running and testing the project while working on it.
- deployment or release: getting it to the people or servers that run it.
  One document per target when there are several (deployment-web,
  deployment-iphone). Procedures for a person; the server side stays with
  the release manager.
- to-do: what was deliberately left out or deferred, with the reasons, so
  they are not lost. Not a prioritized backlog.

Further documents for a single topic are fine (a setup walkthrough for an
external service, a request written for an administrator); name them by
topic and list them in the README.

Each document:

- starts with its title and one or two sentences on what it covers, which
  role it is for, and where the neighboring content lives ("What it does is
  in behavior. Why it is built this way is in design.");
- is named in lowercase with hyphens, with the .md extension;
- links to other documents by file name; when content moves, every link to
  it is updated, including links in code and UI text.


## 4. Markdown that reads as plain text

Documents are .md files, written so they read as well in a terminal (cat,
less, an editor without preview) as they render in a markdown viewer or on
GitHub.

- Headings with #, plain lists with -, numbered lists for procedures.
- Commands, code and file listings in indented or fenced blocks.
  Identifiers, paths and setting names in backticks or on their own line,
  exactly as typed.
- No bold or italic, for emphasis or otherwise. No inline HTML.
- Tables only where a comparison really is tabular, and kept narrow; a wide
  table reads poorly in a terminal.
- Wrap lines at about 80 characters.
- Links as bare file names (design.md) or bare URLs, which read the same in
  both places.

Projects that already use .txt documents keep them until someone decides to
convert; do not mix the two in one project.


## 5. Keep documentation true

- When a code change makes a document wrong, fix the document in the same
  change. Before finishing a task, check the documents that describe what
  changed.
- Record decisions together with their reasons, including options that were
  considered and rejected, so the reasoning is not lost.
- Mark what has not been verified ("Not verified yet: ..."), and keep that
  separate from what has.
- Use absolute dates ("October 2026"), never "recently" or "next year".
- Never put secrets in documentation. Name where a secret is kept instead
  (a gitignored file, the login Keychain, a credentials store).
