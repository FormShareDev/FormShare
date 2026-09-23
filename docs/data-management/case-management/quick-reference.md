---
description: >-
  The screens, the expressions and the rules of case management on one page, for
  when you know the feature and need to remember where something lives.
---

# Quick reference

## Where everything lives

| To do this | Go here |
| --- | --- |
| See, publish, edit or delete lists | Project page, **Published lists** |
| Download ten rows of a list | Published lists, **Sample CSV** |
| Add a column to a case | Published lists, **Properties** |
| Say which list a form follows up | Form page, **Links to lists** |
| See the whole workflow | Project page, **Workflow diagram** |
| See a refused follow-up | Form page, **Manage errors** |

## The usual sequence

1. Upload the case-creating form, test it, [create its repository](../../fundamentals/forms/#create-a-repository).
2. **Published lists** → **Publish a new list** → list code, file name, source form, source table, label column, key, rows to serve → **Publish the list** → add the served columns.
3. Write the follow-up form with `select_one_from_file <file name>`, outside any repeat.
4. Upload it and attach a placeholder CSV for each list it names. The header line is enough.
5. **Links to lists** → confirm the case link → **Save the case link**.
6. Create the follow-up's repository when the form is ready.

## Expressions

**Filter one list by another.** Serve the column to filter on, a plain value or `parent_rowuuid` for the parent's identity, then write the `choice_filter` against the served name:

```
school_id=${school_id}
```

**Read a column of the chosen row.** The instance name is the file name without `.csv`:

```
instance('teacher_list')/root/item[name=current()/../teacher_id]/label
```

**Give the follow-up a primary key.** One submission per case per day:

```
concat(${teacher_id}, '-', format-date(today(), '%Y%m%d'))
```

## Columns every list carries

| Column | What it holds |
| --- | --- |
| `name` | The identity of the row: its [rowuuid](how-cases-are-identified.md) for a list of cases, the value itself for a [value list](published-lists.md#value-lists-and-cascades). |
| `label` | The value of the label column, which is what the enumerator reads. |
| *served columns* | In the order of the list's edit page, under the names you gave them. |

## Columns every repository table carries

| Column | In the main table | In a repeat table |
| --- | --- | --- |
| `rowuuid` | the row's own identity | the row's own identity |
| `parent_rowuuid` | empty | the identity of the row it belongs to |
| `root_rowuuid` | the row's own identity | the identity of the main row of the submission |
| `_active` | 1 unless the case was retired | 1 unless the case was retired |

## Things that cannot be undone

* The **case link**, once the follow-up's repository is built.
* A list's **key**, while a built form uses the list.
* A list's **file name**, in practice, because every form that names it would have to be rewritten and merged.

See "[Rules and safeguards](rules-and-safeguards.md)" for the full set.
