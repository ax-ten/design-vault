# design-vault

An agent skill for keeping a long-lived vault of design decisions coherent as it grows.

```
npx skills add ax-ten/design-vault
```

## What it is for

A design vault is not documentation. Documentation describes something that exists; a design vault
records **what was decided, when, and why**, while the thing being decided is still moving.

The failure mode such a vault falls into is specific, and it is not "the notes get messy". It is that
**the vault starts lying**: two notes say different things, both look current, and nobody — including
the agent reading them — can tell which one the code follows. A vault that lies is worse than no vault,
because it is trusted.

This skill is the set of rules that prevents that. Each one exists because its absence caused a real
failure:

1. **Every decision carries its date** — it is the only chronology a vault has, and it gives you the
   tie-break rule everything else rests on.
2. **Frontmatter moves with the text** — a stale `updated:` field does not make the vault untidy, it
   *disables* the tie-break rule.
3. **A superseding decision deletes the old one** — two versions side by side survive for months and
   neither declares itself the good one.
4. **Never record a verification you did not perform** — one invented fact removes the vault's whole
   value, because now everything has to be re-checked.
5. **One fact, one place** — duplicated facts drift, and then the contradiction is one you created.
6. **Links are the structure** — including links to notes that do not exist yet.
7. **After a full audit, reset the clock** — once everything is reconciled, hundreds of dates become
   noise that hides the next real one.

It also carries procedures for writing a note, superseding a decision, and running an audit that
chases root causes instead of filing their cascades.

## What it is not

It does not index your notes for retrieval, and it does not talk to a vector database. It is a set of
working rules for an agent that writes into, revises, and audits a vault of decisions — the discipline,
not the search.

It is written for Markdown notes with YAML frontmatter and `[[wikilinks]]`, so it fits Obsidian, but
nothing in it is Obsidian-specific.

## License

MIT
