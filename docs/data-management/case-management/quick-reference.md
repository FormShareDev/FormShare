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
| Download ten rows of a list | Published lists, **Sample CSV** or **Sample GeoJSON** |
| Add a column to a case | Published lists, **Properties** |
| Choose a list's key, rows to serve or label | The list's **Edit** page, **Definition** tab |
| Choose the columns a list serves | The list's **Edit** page, **Served columns** tab |
| Narrow the cases a list serves | The list's **Edit** page, **[Filters](list-filters.md)** tab |
| Give each assistant their own cases | The list's **Edit** page, **[Case accessibility](who-receives-which-cases.md)** tab |
| Give a filter to assistants and groups | **Case accessibility** tab, **Add a filter** |
| Export lists to JSON or YAML | Published lists, **Export** on a list, or **Export all** |
| Import lists from another project | Published lists, **Import lists** |
| Say which list a form follows up | Form page, **Links to lists** |
| Change cases when a form is submitted | Form page, **[Form actions](case-actions.md)** |
| Try an action without writing anything | Form actions, **Test** |
| See what the actions did, or retry a failed run | Form actions, **Runs** |
| See what to set up for a form written for ODK Central | Form page, the **Written for ODK Central** box |
| See the whole workflow | Project page, **Workflow diagram** |
| See a refused follow-up | Form page, **Manage errors** |

## The usual sequence

1. Upload the case-creating form, test it, [create its repository](../../fundamentals/forms/#create-a-repository).
2. **Published lists** → **Publish a new list** → list code, format, file name, source form, source table, label column, key or location, rows to serve → **Publish the list** → add the served columns.
3. If you need them, a filter on the **Filters** tab, and who receives which cases on the **Case accessibility** tab.
4. Write the follow-up form with `select_one_from_file <file name>`, outside any repeat.
5. Upload it and attach a placeholder for each list it names: its **Sample CSV** or **Sample GeoJSON**, or a CSV with the header line only.
6. **Links to lists** → confirm the case link → **Save the case link**.
7. Create the follow-up's repository when the form is ready.
8. **Form actions** → **Add an action** → test it.

## Expressions

**Filter one list by another.** Serve the column to filter on, a plain value or `parent_rowuuid` for the parent's identity, then write the `choice_filter` against the served name:

```
school_id=${school_id}
```

**Read a column of the chosen row.** The instance name is the file name without `.csv`:

```
instance('teacher_list')/root/item[name=current()/../teacher_id]/label
```

**Read a column of a case chosen from a GeoJSON list.** The instance name is the file name without `.geojson`, and the identity is `id`:

```
instance('school_map')/root/item[id=current()/../school_id]/district
```

**Pick a case on a map.** Type `select_one_from_file school_map.geojson`, appearance `map`.

**Give the follow-up a primary key.** One submission per case per day:

```
concat(${teacher_id}, '-', format-date(today(), '%Y%m%d'))
```

## Columns every list carries

| Column | What it holds |
| --- | --- |
| `name` | The identity of the row: its [rowuuid](how-cases-are-identified.md) for a list of cases, the value itself for a [value list](published-lists.md#value-lists-and-cascades). |
| `label` | The value of the label column, which is what the enumerator reads. |
| *served columns* | In the order of the list's Served columns tab, under the names you gave them. |

A GeoJSON list carries the identity as each feature's `id`, the label as both `title` and `label`, and the served columns as the feature's properties.

## Columns every repository table carries

| Column | In the main table | In a repeat table |
| --- | --- | --- |
| `rowuuid` | the row's own identity | the row's own identity |
| `parent_rowuuid` | empty | the identity of the row it belongs to |
| `root_rowuuid` | the row's own identity | the identity of the main row of the submission |
| `_active` | 1 unless the case was retired | 1 unless the case was retired |

The main table of a repository that records assignments also carries `_assigned_to`: the assistant a case is assigned to, which a case in a repeat takes from its submission.

## What an action can change

| Target | Rule | Code |
| --- | --- | --- |
| A property of the case | **set property** | `api.case.set("name", value)` |
| A column of the case | **set column** | `api.case.set("column", value)` |
| The case's active flag | **activate or deactivate** | `api.case.deactivate()`, `api.case.activate()` |
| A property of the case's parent, or its active flag | *the case's parent* | `api.case.parent.set(...)`, `.deactivate()`, `.activate()` |

When two actions change the same thing, the one higher in the list wins. If one action fails, none is applied.

## Things that cannot be undone

* The **case link**, once the follow-up's repository is built.
* A list's **key** and its **rows to serve**, while a built form uses the list.
* A list's **file name**, in practice, because every form that names it would have to be rewritten and merged.

See "[Rules and safeguards](rules-and-safeguards.md)" for the full set.
