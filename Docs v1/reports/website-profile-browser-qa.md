# Website profile manual QA

Used the owned tests/browser-fixture.php server only. No default/personal/legacy
state, native game connection or external account/service was used.

- Existing synthetic website player had private defaults. Saved a display name
  and multiline bio as Public; actual public link showed the three website fields.
- Changed back to Private; subsequent public GET returned 404 without the alias.
- Preview saved profile showed the owner's private fields and clear private-preview
  labelling; public lookup stayed unavailable. Preview did not publish or edit.
- Registered qa_ser-1 through the real form, signed out the original player and
  signed in with an uppercase form of that username. Edited/published its website
  profile and created an owned private support ticket successfully.
- Final matrix: home, editor, private preview, public opted-in profile, public
  lookup landing and unmatched synthetic native dossier at 1440x1000 and 390x844.
  All twelve checks had no horizontal overflow or inline style attributes.
- Final browser console collection contained zero errors/warnings. Visually
  inspected the mobile preview and editor screenshots in this reports directory.

An early automation lookup used Username on the registration form, which actually
labels that field Account username. No request was submitted by that failed
lookup; inspecting labels and rerunning completed the workflow.

This is manual QA, not a committed browser regression suite or template-editor
round-trip evidence. Canonical verification and fixture cleanup belong in STATE.md.
