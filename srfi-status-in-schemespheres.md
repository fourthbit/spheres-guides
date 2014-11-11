Status of SRFI's in SchemeSpheres
=================================

This is a list of the current status of the [Scheme Requests for Implementation](http://srfi.schemers.org/) in SchemeSpheres:

    SRFI-0   [NATIVE] Native in Gambit, but uses syntax-rules implementation for macro-expansion time
    SRFI-1   [SPHERE] Included in (fabric: algorithm/list)
    SRFI-2   [SPHERE] Included in (core: base-macros)
    SRFI-3   [WITHDR] -- Withdrawn from final SRFIs
    SRFI-4   [NATIVE] Native in Gambit
    SRFI-5   [SPHERE] Included in (energy: multiple-values-macros)
    SRFI-6   [NATIVE] Native in Gambit
    SRFI-7   [REJECT] -- Rejected: handled by Sphere's compilation system and Sake scripts
    SRFI-8   [NATIVE] Native in Gambit, but included in (core: base-macros) for expanders
    SRFI-9   [NATIVE] Native in Gambit
    SRFI-10  [REJECT] -- Rejected: needs compiler support
    SRFI-11  [SPHERE] Included in (core: base-macros)
    SRFI-12  [WITHDR] -- Withdrawn from final SRFIs
    SRFI-13  [SPHERE] Included in (string: string), correctly defined for Latin-1 and ASCII, but not for Unicode
    SRFI-14  [SPHERE] Included in (string: char)
    SRFI-15  [WITHDR] -- Withdrawn from final SRFIs
    SRFI-16  [SPHERE] -- Included in (core: base-macros)
    SRFI-17  [REJECT] -- Rejected: needs compiler support
    SRFI-18  [NATIVE] Native in Gambit
    SRFI-19  [SPHERE] -- Included in (energy: time) *** NEEDS REVIEW ***
    SRFI-20  [WITHDR] -- Withdrawn from final SRFIs
    SRFI-21  [NATIVE] Native in Gambit
    SRFI-22  [NATIVE] Native in Gambit
    SRFI-23  [NATIVE] Native in Gambit
    SRFI-24  [WITHDR] -- Withdrawn from final SRFIs
    SRFI-25  [SPHERE] Included in (fabric: structure/multi-dimensional-array)
    SRFI-26  [SPHERE] Included in (core: functional-macros)
    SRFI-27  [NATIVE] Native in Gambit
    SRFI-28  [SPHERE] Included in (string: format)
    SRFI-29  [SPHERE] Included in (energy: localization)
    SRFI-30  [NATIVE] Native in Gambit
    SRFI-31  [SPHERE] Included in (core: base-macros)
    SRFI-32  [WITHDR] -- Withdrawn from final SRFIs
    SRFI-33  [WITHDR] -- Withdrawn from final SRFIs
    SRFI-34  [SPHERE] Included in (energy: exception)
    SRFI-35  [SPHERE] Included in (energy: exception)
    SRFI-36  [SPHERE] Included in (energy: exception)
    SRFI-37  [SPHERE] Included in (energy: program-arguments)
    SRFI-38  [SPHERE] Included in (energy: shared-structure)
    SRFI-39  [NATIVE] Native in Gambit
    SRFI-40  [DEPREC] -- Deprecated in final SRFIs by SRFI-41
    SRFI-41  [SPHERE] Included in (fabric: structure/stream) and (fabric: algorithm/stream)
    SRFI-42  [SPHERE] Included in (fabric: algorithm/comprehension)
    SRFI-43  [SPHERE] Included in (fabric: algorithm/vector)
    SRFI-44  [REJECT] -- Rejected: we handle each collection on its own
    SRFI-45  [SPHERE] -- Deprecated in final SRFIs by SRFI-41
    SRFI-46  [SPHERE] Included as part of the current macro expander (Alexpander)
    SRFI-47  [DEPREC] -- Deprecated in final SRFIs by SRFI-63
    SRFI-48  [SPHERE] Included in (string: format)
    SRFI-49  [REJECT] -- Rejected: use Python?
    SRFI-50  [WITHDR] -- Withdrawn from final SRFIs
    SRFI-51  [SPHERE] Included in (energy: rest-values)
    SRFI-52  [WITHDR] -- Withdrawn from final SRFIs
    SRFI-53  [WITHDR] -- Withdrawn from final SRFIs
    SRFI-54  [SPHERE] Included in (string: format)
    SRFI-55  [REJECT] -- Rejected: use Sphere's dependency handling system
    SRFI-56  [WITHDR] -- Withdrawn from final SRFIs
    SRFI-57  [REJECT] -- Rejected: either use SRFI-9 or an OO system from Object Sphere
    SRFI-58  [REJECT] -- Rejected: needs compiler support
    SRFI-59  [REJECT] -- Rejected: not really useful, especially for Sphere's OS targets
    SRFI-60  [SPHERE] Partially native in gambit, completed in (math: bit-logical)
    SRFI-61  [SPHERE] Implemented by Riaxpander
    SRFI-62  [NATIVE] Native in Gambit
    SRFI-63  [SPHERE] Included in (fabric: structure/array) doesn't support notation from SRFI-58
    SRFI-64  [SPHERE] Included in (energy: testing)
    SRFI-65  [WITHDR] -- Withdrawn from final SRFIs
    SRFI-66  [SPHERE] Included in (fabric: algorithm/u8vector), shares API with SRFI-4 (native in Gambit)
    SRFI-67  [SPHERE] Included in (fabric: algorithm/compare)
    SRFI-68  [WITHDR] -- Withdrawn from final SRFIs
    SRFI-69  [SPHERE] Included in (fabric: structure/hash-table)
    SRFI-70  [REJECT] -- Rejected: use native numeric computation features provided buy Gambit
    SRFI-71  [SPHERE] Included in (energy: let-multiple-macros)
    SRFI-72  [REJECT] -- Rejected: not supported by current macro expander (Alexpander)
    SRFI-73  [WITHDR] -- Withdrawn from final SRFIs
    SRFI-74  [REJECT] -- Rejected: use native Gambit support for byte vectors
    SRFI-75  [WITHDR] -- Withdrawn from final SRFIs
    SRFI-76  [WITHDR] -- Withdrawn from final SRFIs
    SRFI-77  [WITHDR] -- Withdrawn from final SRFIs
    SRFI-78  [SPHERE] Included in (core: testing)
    SRFI-79  [WITHDR] -- Withdrawn from final SRFIs
    SRFI-80  [WITHDR] -- Withdrawn from final SRFIs
    SRFI-81  [WITHDR] -- Withdrawn from final SRFIs
    SRFI-82  [WITHDR] -- Withdrawn from final SRFIs
    SRFI-83  [WITHDR] -- Withdrawn from final SRFIs
    SRFI-84  [WITHDR] -- Withdrawn from final SRFIs
    SRFI-85  [WITHDR] -- Withdrawn from final SRFIs
    SRFI-86  [REJECT] -- Rejected: it is complex, no compelling advantages over the standard mechanisms
    SRFI-87  [SPHERE] Included in (core: base-macros)
    SRFI-88  [NATIVE] Native in Gambit
    SRFI-89  [SPHERE] Included as part of macro expansion
    SRFI-90  [NATIVE] Functionality is native in Gambit
    SRFI-91  [WITHDR] -- Withdrawn from final SRFIs
    SRFI-92  [WITHDR] -- Withdrawn from final SRFIs
    SRFI-93  [WITHDR] -- Withdrawn from final SRFIs
    SRFI-94  [REJECT] -- Rejected: use native numeric computation features provided by Gambit
    SRFI-95  [SPHERE] Included in (fabric: algorithm/sort-merge)
    SRFI-96  [REJECT] -- Rejected: Spheres doesn't aim to be a SLIB-compatible library
    SRFI-97  [REJECT] -- Rejected: not compatible with Spheres
    SRFI-98  [NATIVE] Partial support by Gambit, lacks full environment listing
    SRFI-99  [SPHERE] -- Included in (object: record)
    SRFI-100 [REJECT] -- Rejected: either use SRFI-9 or an OO system from Object Sphere
    SRFI-101 [DRAFTS] -- *** PENDING IMPLEMENTATION ***
    SRFI-102 [WITHDR] -- Withdrawn from final SRFIs
    SRFI-103 [WITHDR] -- Withdrawn from final SRFIs
    SRFI-104 [WITHDR] -- Withdrawn from final SRFIs
    SRFI-105 [REJECT] -- Rejected: non-S-expression syntax is out of the scope of Spheres
    SRFI-106 [DRAFTS] -- *** PENDING IMPLEMENTATION ***
    SRFI-107 [DRAFTS] -- Draft
    SRFI-108 [REJECT] -- Rejected: reader extensions are out of the scope of Spheres
    SRFI-109 [REJECT] -- Rejected: reader extensions are out of the scope of Spheres
    SRFI-110 [REJECT] -- Rejected: non-S-expression syntax i out of the scope of Spheres
    SRFI-111 [NATIVE] -- Native in Gambit

