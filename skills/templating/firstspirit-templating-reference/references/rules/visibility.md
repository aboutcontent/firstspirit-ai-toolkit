# Rules: Visibility Patterns

Correct, tested patterns for FirstSpirit visibility rules.

---

## Show/hide by selection (radio/combobox)

```xml
<RULE>
    <WITH>
        <EQUAL>
            <PROPERTY source="selection" name="ENTRY"/>
            <TEXT>1</TEXT>
        </EQUAL>
    </WITH>
    <DO>
        <PROPERTY source="st_A" name="VISIBLE"/>
    </DO>
</RULE>
<RULE>
    <WITH>
        <EQUAL>
            <PROPERTY source="selection" name="ENTRY"/>
            <TEXT>2</TEXT>
        </EQUAL>
    </WITH>
    <DO>
        <PROPERTY source="st_B" name="VISIBLE"/>
    </DO>
</RULE>
```

## Show/hide by input state

```xml
<RULE>
    <WITH>
        <PROPERTY name="EMPTY" source="st_text_1"/>
    </WITH>
    <DO>
        <PROPERTY name="VISIBLE" source="st_text_2"/>
        <NOT>
            <PROPERTY name="VISIBLE" source="st_text_3"/>
        </NOT>
    </DO>
</RULE>
```

Logic: IF text_1 empty -> show text_2, hide text_3.

## Gate on a toggle — compare, never test the bare value

A `CMS_INPUT_TOGGLE` has **three** states: `true`, `false` and `null` before the editor has touched
it (ODFS: *"The initial state (no option has been selected yet) returns the value `null`. This
should be taken into account when defining rules, especially when using the property VISIBLE."*).
A rule whose whole condition is the toggle's bare `VALUE` therefore holds in **neither** direction
on a fresh section, and `VISIBLE` hides the governed fields "in all other cases" — so the fields
are invisible until the editor finds and flips a toggle they cannot see the point of.

```xml
<!-- ✗ never fires on a new section -->
<RULE>
    <WITH><PROPERTY name="VALUE" source="st_show_cta"/></WITH>
    <DO><PROPERTY name="VISIBLE" source="st_cta_label"/></DO>
</RULE>

<!-- ✓ compare against the boolean constant -->
<RULE>
    <WITH>
        <EQUAL>
            <PROPERTY name="VALUE" source="st_show_cta"/>
            <TRUE/>
        </EQUAL>
    </WITH>
    <DO><PROPERTY name="VISIBLE" source="st_cta_label"/></DO>
</RULE>
```

`<TRUE/>` and `<FALSE/>` are the rule constants for booleans (`<TEXT/>` and `<NUMBER/>` are the
others); do not write `<TEXT>true</TEXT>`. `<NOT_NULL/>` answers a different question ("has any
value") and cannot tell `true` from `false`. The same applies to every component whose value can
be unset — `CMS_INPUT_CHECKBOX`, `CMS_INPUT_RADIOBUTTON`, `CMS_INPUT_COMBOBOX`, `CMS_INPUT_LIST`:
compare against a constant or test `EMPTY`, never gate on the bare value. `[odfs]`

## Address a group, not each of its fields

When one condition governs every member of a `CMS_GROUP`, write **one** rule against the group.
`source` reaches a design component through `#form.<name>`:

```xml
<RULE>
    <WITH>
        <EQUAL>
            <PROPERTY name="VALUE" source="st_show_cta"/>
            <TRUE/>
        </EQUAL>
    </WITH>
    <DO><PROPERTY name="VISIBLE" source="#form.cg_cta"/></DO>
</RULE>
```

Five identical `VISIBLE` rules for the five fields of one group is the shape a generator produces;
one rule on the group is the shape hand-written projects use. Per-field rules are right only when
the members are governed differently. `[odfs]` (`<PROPERTY source="#form.gadget"/>` for design
components)

## Store-based visibility (page store only)

```xml
<RULE>
    <WITH>
        <EQUAL>
            <PROPERTY source="#global" name="STORETYPE"/>
            <TEXT>pagestore</TEXT>
        </EQUAL>
    </WITH>
    <DO>
        <PROPERTY source="#form.st_pagestore" name="VISIBLE"/>
    </DO>
</RULE>
```

## Multi-store visibility

```xml
<RULE>
    <WITH>
        <OR>
            <EQUAL>
                <PROPERTY source="#global" name="STORETYPE"/>
                <TEXT>pagestore</TEXT>
            </EQUAL>
            <EQUAL>
                <PROPERTY source="#global" name="STORETYPE"/>
                <TEXT>mediastore</TEXT>
            </EQUAL>
        </OR>
    </WITH>
    <DO>
        <PROPERTY source="st_keywords" name="VISIBLE"/>
    </DO>
</RULE>
```

## Language-based visibility

```xml
<RULE>
    <WITH>
        <EQUAL>
            <PROPERTY source="#global" name="LANG"/>
            <TEXT>DE</TEXT>
        </EQUAL>
    </WITH>
    <DO>
        <PROPERTY source="#form.st_german" name="VISIBLE"/>
    </DO>
</RULE>
```

## ContentCreator only (WEB)

```xml
<RULE>
    <WITH>
        <PROPERTY source="#global" name="WEB"/>
    </WITH>
    <DO>
        <PROPERTY source="#form.st_WebClient_only" name="VISIBLE"/>
    </DO>
</RULE>
```

## SiteArchitect only (NOT WEB)

```xml
<RULE>
    <WITH>
        <NOT>
            <PROPERTY source="#global" name="WEB"/>
        </NOT>
    </WITH>
    <DO>
        <PROPERTY source="#form.st_JavaClient_only" name="VISIBLE"/>
    </DO>
</RULE>
```

## Permission-based visibility (user group)

`<IN_GROUP name="…"/>` tests whether the current editor belongs to a server user group — used to
show a field only to certain editors:

```xml
<RULE>
    <WITH>
        <IN_GROUP name="Administrators"/>
    </WITH>
    <DO>
        <PROPERTY source="#form.st_advanced" name="VISIBLE"/>
    </DO>
</RULE>
```

## Event-triggered rules (`<ON_EVENT>`, `<ON_SAVE>`, `<ON_RELEASE>`)

Besides `<RULE>`, a ruleset can carry an `<ON_EVENT>` block that fires on a form event (rather than
continuously). It wraps the same `<WITH>`/`<DO>` and is used, for example, to toggle `VISIBLE` when
a field changes. You will encounter it in exported rulesets alongside `<RULE>`; read it the same way
(the `<DO>` acts when `<WITH>` holds).

The rule parser accepts three such blocks directly under `<RULES>`, one per validation scope
`[core]`: `<ON_EVENT>`, `<ON_SAVE>` and `<ON_RELEASE>`. Any other tag name at that level is a
parsing error. A block pre-sets the scope for the `<VALIDATION>`s inside it, so a `<VALIDATION>`
without a `scope` attribute inside `<ON_SAVE>` blocks on save, and an explicit `scope` can only
**raise** the level (INFO → SAVE → RELEASE), never lower it below the block's. `<ON_SAVE>` and
`<ON_RELEASE>` are not on the ODFS *rule execution time* page, which documents only
`<RULE when="…">` `[verify]`: prefer `<RULE when="ONSAVE">` with an explicit `scope` in new
rulesets, and read the block forms when you meet them in exports.

## Section inclusion visibility

```xml
<RULE>
    <WITH>
        <AND>
            <EQUAL>
                <PROPERTY source="#global" name="STORETYPE"/>
                <TEXT>pagestore</TEXT>
            </EQUAL>
            <PROPERTY source="#global" name="INCLUDED"/>
        </AND>
    </WITH>
    <DO>
        <PROPERTY source="#form.A" name="VISIBLE"/>
    </DO>
</RULE>
```
