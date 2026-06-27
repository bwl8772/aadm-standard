# SURF checklist — one UI surface column

Copy this template per named surface (page, editor, wizard step). Parent program: [Signal Stack compliance](../standards/signal-stack-compliance.md).

---

**Surface:** _________________________  
**UDALI primary band:** __ Designer | Adapter | Logician | Integrator | Unifier  
**Route / entry:** _________________________  
**Read contract (file):** _________________________  
**Write / mutation surface:** _________________________  
**Status:** draft | ready for attestation  

---

| SIGL | Layer | Evidence (path:line / gap / n/a / partial) |
|------|-------|---------------------------------------------|
| SIGL-01 | Database column | |
| SIGL-02 | Schema mapping | |
| SIGL-03 | Typed contract / DTO | |
| SIGL-04 | Registry / enum | |
| SIGL-05 | Write command + conflict codes | |
| SIGL-06 | Read query | |
| SIGL-07 | Read-model service | |
| SIGL-08 | Verdict producer | |
| SIGL-09 | Action eligibility | |
| SIGL-10 | Single client mapper | |
| SIGL-11 | UI component(s) | |
| SIGL-12 | Visibility / reveal overlay | |

---

## Invariants (attest before ship)

- [ ] **SIGL-08:** One server verdict producer for this product policy (or **gap** + owner).  
- [ ] **SIGL-10:** One resolver policy—deletion test passes (or **gap** + migration plan).  
- [ ] **SIGL-11:** UI is a dumb renderer—no composed severity or next-action logic.  
- [ ] **SIGL-12:** Reveal rules trace to server signals only.  

---

**Reviewer / date / system of record:** _________________________
