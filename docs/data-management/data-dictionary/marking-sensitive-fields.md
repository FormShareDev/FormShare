---
description: Mark fields as sensitive so they are excluded, recoded, or unlinked when public data products are generated. Private products always retain the original values.
---

# Marking fields as sensitive

A form often contains data that you don't want to publish without care — names, national IDs, phone numbers, exact GPS coordinates, household addresses, individual answers tied to identifiable people. FormShare lets you flag these fields as **sensitive** in the [data dictionary](README.md). Sensitive fields are then handled differently when public [data products](../data-products/) are generated, while remaining fully available in private products and in the curation interfaces.

This is the privacy-by-design knob in FormShare: decide once which fields are sensitive, and every future export respects that decision automatically.

## What sensitivity does

Marking a field sensitive changes how the field appears in **public** products only:

* It does **not** affect private products. Private products are exports for the project team and always include sensitive fields in full.
* It does **not** change the value stored in the repository. The original value is preserved; sensitivity is applied at export time.
* It does **not** restrict the [web cleaning interface](../cleaning/in-a-repository/web-interface.md) or the [API](../cleaning/in-a-repository/api.md). Assistants with the "Can clean" permission still see and edit the real values.

What changes is how the value appears in the public exports your [partners](../../fundamentals/partners.md) and downstream consumers receive.

## The three protection types

When you mark a field sensitive, FormShare asks how the field should be protected. There are three choices:

* **Exclude** — the field is removed entirely from the public product. The column doesn't appear in CSV; the key doesn't appear in JSON. This is the default for ordinary fields and the right choice for most identifiers (names, ID numbers, phone numbers, addresses, raw GPS coordinates).
* **Recode** — the field's values are replaced with sequential codes. The first distinct value becomes `1`, the second becomes `2`, and so on. The column is still present, so analysts can still group, count, and join — they just can't trace a row back to the real subject. This is the default for **primary key** fields, where you typically need a join key but not the original identifier.
* **Unlink** — the field's link to a lookup or related table is broken (the row appears, but the foreign key column is null). This is used for lookup relationships where the relationship itself is identifying — relatively rare; FormShare picks it as the default for lookup-relation fields.

If you're not sure which to pick, **Exclude** is the safest default for most identifiers — it leaves no trace of the value in the public product.

## How to mark a field

1. Open the [data dictionary](README.md) for the form: from the form details page, click **Data dictionary**.
2. Click the table that contains the field. The fields view appears.
3. Find the field. Each field card shows a coloured circle — **green** for not sensitive, **red** for sensitive.
4. Click **Set as sensitive** (the eye-slash icon) on the card. A modal dialog opens with the **Type of protection** selector.

<!-- TODO screenshot: the "Set as sensitive" modal showing the protection type selector and the Set / Cancel buttons. -->

5. Pick the protection type (the default is usually right). Click **Set as sensitive** in the modal.

The page reloads and the field's indicator turns red. The form's underlying schema is updated immediately, so the next public export will apply the protection.

## How to unmark a field

To remove the sensitive flag from a field, open the data dictionary, find the field, and click **Set as not sensitive** (the eye icon) on the field card. There is no confirmation dialog — the field is unflagged immediately and subsequent public exports will include it in full.

{% hint style="warning" %}
**Be deliberate when unmarking**. If public products of this form have already been distributed with the field excluded, unmarking the field changes what future products contain — but it doesn't recall the previously distributed product. Make sure you really want the field visible from now on.
{% endhint %}

## Encrypted fields

If the underlying XLSForm marks a field as encrypted, FormShare treats the field as implicitly sensitive: the indicator is red and the protection is shown as **encrypted**. These fields **cannot** be unmarked from the FormShare UI — the encryption is built into the form's design and survives in every export.

## Where sensitivity is stored

Sensitivity is recorded **per field, per table** in the form's data dictionary. The same field name living in two different tables (rare, but possible with repeats) is treated as two separate decisions. FormShare also writes the sensitivity flag into the form's internal schema file (`create.xml`) so that tools that work directly with the schema (some plugins, custom exporters) see the same view of which fields are sensitive.

## Useful patterns

* **Anonymising names**: Mark the person's name field as sensitive with **Exclude**. The name vanishes from public exports.
* **Producing analysable data without identifiers**: Mark the primary key as **Recode** and every other identifier as **Exclude**. Public exports keep a row-level join key but cannot be traced to subjects.
* **Sharing aggregates only**: Mark every individual-level identifier as **Exclude**. Combine with [partner](../../fundamentals/partners.md) project links to deliver public products to external consumers.

## What's next

* "[The data dictionary](README.md)" covers how to navigate tables and fields and what other metadata you can edit.
* "[Private vs public products](../data-products/private-vs-public-products.md)" explains the public/private distinction in more detail.
* "[Partners](../../fundamentals/partners.md)" describes how external data consumers access public products.
