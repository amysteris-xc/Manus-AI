# Curriculum State

## Course configuration

| Setting | Value |
|---|---|
| Tracks | Russian and C programming |
| Delivery rhythm | Alternating tracks; one lesson per scheduled run |
| Intended lesson duration | 25–35 minutes |
| C language baseline | ISO C17 unless a lesson explicitly says otherwise |
| Course root | Repository root |

## Current position

C lesson 201 is complete. **Do not create lesson 202 automatically.** The scheduled task must inspect the course state and report that the prepared curriculum is complete until an explicit extension or remediation request is recorded.

| Status | Lesson identifier | Topic | Prerequisites |
|---|---|---|---|
| Completed | `russian-001-cyrillic-greetings` | Cyrillic orientation, basic pronunciation, greetings, and first recall practice | None |
| Completed | `c-001-build-types-output` | C program structure, compiling, fundamental types, and output | None |
| Completed | `russian-002-sounds-and-introductions` | High-value Cyrillic sound patterns, stress awareness, and fuller introductions | Russian 001 |
| Completed | `c-002-control-flow-and-simple-input` | Expressions, decisions, loops, and a first checked input boundary | C 001 |
| Completed | `russian-003-gender-and-everyday-nouns` | Grammatical gender, basic noun patterns, and familiar objects | Russian 001–002 |
| Completed | `c-003-functions-and-error-returns` | Function boundaries, parameters, return values, and explicit error propagation | C 001–002 |
| Completed | `russian-004-adjectives-and-simple-descriptions` | Gender agreement in common adjectives and concise object descriptions | Russian 001–003 |
| Completed | `c-004-arrays-strings-and-bounds` | Fixed-size arrays, C string termination, length limits, and index validation | C 001–003 |
| Completed | `russian-005-present-tense-verbs-and-routines` | High-frequency present-tense verbs and short daily-routine statements | Russian 001–004 |
| Completed | `c-005-array-parameters-and-bounded-iteration` | Array parameters, pointer relationships, `size_t` counts, and bounded traversal | C 001–004 |
| Completed | `russian-006-places-and-location-phrases` | Common places, `где?` questions, and basic location phrases | Russian 001–005 |
| Completed | `c-006-unsigned-integers-and-byte-arrays` | `uint8_t`, byte arrays versus strings, explicit lengths, and bounded byte traversal | C 001–005 |
| Completed | `russian-007-questions-and-basic-negation` | Yes/no questions, `не`, and concise responses about routines and locations | Russian 001–006 |
| Completed | `c-007-bitwise-operations-and-uint32-t` | `uint32_t`, masks, shifts, and bounded word-oriented data handling | C 001–006 |
| Completed | `russian-008-possessives-and-everyday-people` | Possessive pronouns, familiar people, and short ownership statements | Russian 001–007 |
| Completed | `c-008-byte-order-and-explicit-serialization` | Byte order and explicit `uint8_t`/`uint32_t` serialization | C 001–007 |
| Completed | `russian-009-plural-nouns-and-counting-small-groups` | Plural noun packages and statements about small groups | Russian 001–008 |
| Completed | `c-009-robust-binary-file-copying` | Robust binary file copying with checked reads and partial writes | C 001–008 |
| Completed | `russian-010-accusative-inanimate-direct-objects` | Inanimate direct objects in the accusative | Russian 001–009 |
| Completed | `c-010-bounded-chunk-file-statistics` | Bounded binary chunk processing and checked file statistics | C 001–009 |
| Completed | `russian-011-time-words-and-daily-schedule` | Time words and concise daily routine statements | Russian 001–010 |
| Completed | `c-011-strict-hexadecimal-parsing-and-fields` | Strict hexadecimal parsing and validated word fields | C 001–010 |
| Completed | `russian-012-past-tense-singular-and-gender` | Singular past tense with gender-aware forms | Russian 001–011 |
| Completed | `c-012-checked-dynamic-byte-buffers` | Dynamic byte buffers with checked allocation growth | C 001–011 |
| Completed | `russian-013-past-tense-plural-and-people` | Plural past tense and general group statements | Russian 001–012 |
| Completed | `c-013-safe-uint32-rotations-and-test-vectors` | Safe `uint32_t` rotations and fixed test vectors | C 001–012 |
| Completed | `russian-014-future-plans-with-budu-and-infinitives` | Simple future plans with `буду` and infinitives | Russian 001–013 |
| Completed | `c-014-checksums-and-noncryptographic-integrity` | Toy checksums, test vectors, and non-cryptographic limitations | C 001–013 |
| Completed | `russian-015-ability-with-mogu-and-mozhesh` | Ability with `могу`, `можешь`, and `можете` | Russian 001–014 |
| Completed | `c-015-dynamic-file-reading-with-checked-growth` | Dynamic file reading with bounded chunks and checked growth | C 001–014 |
| Completed | `russian-016-wants-with-khochu-and-infinitives` | Wants with `хочу` and familiar infinitives | Russian 001–015 |
| Completed | `c-016-fixed-header-serialization-round-trips` | Fixed-header serialization with byte order and round-trip tests | C 001–015 |
| Completed | `russian-017-integrated-routines-past-present-and-future` | Integrated routine statements across past, present, and future | Russian 001–016 |
| Completed | `c-017-fixed-binary-records-and-truncation` | Fixed binary records with checked decoding and truncation handling | C 001–016 |
| Completed | `russian-018-directions-and-general-places` | Directions and general place phrases | Russian 001–017 |
| Completed | `c-018-bounded-binary-record-writing` | Bounded binary record writing with checked output results | C 001–017 |
| Completed | `russian-019-motion-with-v-and-na` | Basic motion with `в`, `на`, and `куда?` | Russian 001–018 |
| Completed | `c-019-append-only-binary-output` | Append-only binary output with checked writes | C 001–018 |
| Completed | `russian-020-numbers-one-to-ten-and-noun-awareness` | Numbers one to ten and controlled noun awareness | Russian 001–019 |
| Completed | `c-020-counted-byte-copy-and-equality` | Counted byte copying and equality without sentinels | C 001–019 |
| Completed | `russian-021-family-and-people-vocabulary` | Family and people vocabulary with neutral possessive statements | Russian 001–020 |
| Completed | `c-021-checked-uint32-arithmetic` | Checked `uint32_t` arithmetic and overflow rejection | C 001–020 |
| Completed | `russian-022-weather-and-basic-adjectives` | General weather descriptions and basic adjective review | Russian 001–021 |
| Completed | `c-022-byte-histograms-with-bounds` | Byte histograms with bounded counters | C 001–021 |
| Completed | `russian-023-food-and-simple-requests` | Food words and simple, general requests | Russian 001–022 |
| Completed | `c-023-length-prefixed-byte-buffers` | Length-prefixed byte buffers and input validation | C 001–022 |
| Completed | `russian-024-days-of-week-and-routines` | Days of the week and fictional routine statements | Russian 001–023 |
| Completed | `c-024-bounded-binary-frame-decoding` | Validated decoding of bounded binary frames | C 001–023 |
| Completed | `russian-025-comparatives-with-bolshe-i-menshe` | Controlled comparisons with `больше` and `меньше` | Russian 001–024 |
| Completed | `c-025-streaming-byte-processing` | Streaming byte processing with bounded accumulation | C 001–024 |
| Completed | `russian-026-polite-requests-and-please` | Polite requests and `пожалуйста` | Russian 001–025 |
| Completed | `c-026-counted-byte-equality` | Counted byte equality with embedded zero bytes | C 001–025 |
| Completed | `russian-027-cumulative-conversation-and-review` | Cumulative fictional conversation and review | Russian 001–026 |
| Completed | `c-027-toy-mixing-and-test-vectors` | Insecure toy mixing and fixed test vectors | C 001–026 |
| Completed | `russian-028-likes-and-basic-preferences` | Likes, Dislikes, and Basic Preferences | Russian 001–027 |
| Completed | `c-028-structured-text-and-byte-boundaries` | Structured Text and Byte Boundaries | C 001–027 |
| Completed | `russian-029-telling-time-on-the-hour` | Telling Time on the Hour | Russian 001–028 |
| Completed | `c-029-hexadecimal-decoding-with-bounds` | Hexadecimal Decoding with Bounds | C 001–028 |
| Completed | `russian-030-months-and-seasons-overview` | Months and Seasons Overview | Russian 001–029 |
| Completed | `c-030-checked-byte-array-reverse` | Checked Byte Array Reversal | C 001–029 |
| Completed | `russian-031-colors-and-object-agreement` | Colors and Object Agreement | Russian 001–030 |
| Completed | `c-031-counted-byte-substring-search` | Counted Byte Substring Search | C 001–030 |
| Completed | `russian-032-conjunctions-i-a-and-no` | Connecting Ideas with `и`, `а`, and `но` | Russian 001–031 |
| Completed | `c-032-counted-run-length-byte-decoding` | Counted Run-Length Byte Decoding | C 001–031 |
| Completed | `russian-033-modes-of-transport-with-na` | Modes of Transport with `на` | Russian 001–032 |
| Completed | `c-033-multi-field-binary-record-validation` | Multi-Field Binary Record Validation | C 001–032 |
| Completed | `russian-034-shopping-and-skolko-stoit` | Shopping Inquiries with `Сколько стоит?` | Russian 001–033 |
| Completed | `c-034-sliding-window-byte-matching` | Sliding Window Byte Matching | C 001–033 |
| Completed | `russian-035-adverbs-of-frequency` | Adverbs of Frequency | Russian 001–034 |
| Completed | `c-035-fixed-size-circular-byte-buffer` | Fixed-Size Circular Byte Buffer | C 001–034 |
| Completed | `russian-036-giving-simple-instructions-imperatives` | Giving Simple Instructions with Imperatives | Russian 001–035 |
| Completed | `c-036-prefix-length-matching-on-byte-slices` | Prefix Length Matching on Byte Slices | C 001–035 |
| Completed | `russian-037-languages-and-speaking-po-russki` | Languages and Speaking with `по-русски` | Russian 001–036 |
| Completed | `c-037-checked-variable-byte-integer-encoding` | Checked Variable-Byte Integer Encoding | C 001–036 |
| Completed | `russian-038-spatial-prepositions-okolo-and-ryadom` | Spatial Relations with `около` and `рядом` | Russian 001–037 |
| Completed | `c-038-checked-variable-byte-integer-decoding` | Checked Variable-Byte Integer Decoding | C 001–037 |
| Completed | `russian-039-professions-and-occupations` | Professions and Occupations | Russian 001–038 |
| Completed | `c-039-simple-bit-level-parity-calculation` | Simple Bit-Level Parity Calculation | C 001–038 |
| Completed | `russian-040-expressing-reasons-with-potomu-chto` | Expressing Reasons with `потому что` | Russian 001–039 |
| Completed | `c-040-lookup-table-bit-count-calculation` | Lookup-Table Bit-Count Calculation | C 001–039 |
| Completed | `russian-041-body-parts-and-physical-states` | Body Parts and Physical States | Russian 001–040 |
| Completed | `c-041-byte-wise-xor-masking-and-unmasking` | Byte-Wise XOR Masking and Unmasking | C 001–040 |
| Completed | `russian-042-expressing-agreement-and-opinion` | Expressing Agreement and Opinion | Russian 001–041 |
| Completed | `c-042-non-cryptographic-crc8-computation` | Non-Cryptographic CRC-8 Computation | C 001–041 |
| Completed | `russian-043-seasons-and-weather-nuances` | Weather Nuances and Climate Descriptions | Russian 001–042 |
| Completed | `c-043-table-driven-crc8-verification` | Table-Driven CRC-8 Verification | C 001–042 |
| Completed | `russian-044-post-office-and-letters-vocabulary` | Letters, Messages, and Delivery | Russian 001–043 |
| Completed | `c-044-counted-byte-buffer-deduplication` | Counted Byte Buffer Deduplication | C 001–043 |
| Completed | `russian-045-sports-and-exercise-routines` | Sports and Exercise Routines | Russian 001–044 |
| Completed | `c-045-binary-key-value-frame-serialization` | Binary Key-Value Frame Serialization | C 001–044 |
| Completed | `russian-046-rooms-in-a-house-and-furniture` | Rooms in a House and Furniture | Russian 001–045 |
| Completed | `c-046-binary-key-value-frame-parsing` | Binary Key-Value Frame Parsing | C 001–045 |
| Completed | `russian-047-clothing-and-dressing-verbs` | Clothing Words and Simple Descriptions | Russian 001–046 |
| Completed | `c-047-checked-big-endian-64bit-integer-encoding` | Checked Big-Endian 64-Bit Integer Encoding | C 001–046 |
| Completed | `russian-048-shopping-for-clothes-and-size` | Shopping for Clothes and Size Inquiries | Russian 001–047 |
| Completed | `c-048-checked-big-endian-64bit-integer-decoding` | Checked Big-Endian 64-Bit Integer Decoding | C 001–047 |
| Completed | `russian-049-music-art-and-cultural-interests` | Music, Art, and Cultural Interests | Russian 001–048 |
| Completed | `c-049-constant-time-byte-buffer-equality-check` | Constant-Time Byte Buffer Equality Check | C 001–048 |
| Completed | `russian-050-travel-plans-and-buying-tickets` | Travel Plans and Buying Tickets | Russian 001–049 |
| Completed | `c-050-toy-sponge-mixing-and-security-limits` | Toy Sponge Mixing and Security Limits | C 001–049 |
| Completed | `russian-051-hotels-and-accommodation` | Hotels and Accommodation | Russian 001–050 |
| Completed | `c-051-fixed-size-fifo-byte-queue` | Fixed-Size FIFO Byte Queue | C 001–050 |
| Completed | `russian-052-ordering-in-a-cafe-and-bill` | Ordering in a Cafe and Asking for the Bill | Russian 001–051 |
| Completed | `c-052-byte-array-compaction-and-filtering` | Byte Array Compaction and Filtering | C 001–051 |
| Completed | `russian-053-asking-for-help-and-clarification` | Asking for Help and Clarification | Russian 001–052 |
| Completed | `c-053-bitset-implementation-on-uint8-arrays` | Bitset Implementation on uint8_t Arrays | C 001–052 |
| Completed | `russian-054-future-intentions-with-planiruyu` | Future Intentions with `я планирую` | Russian 001–053 |
| Completed | `c-054-simple-base64-encoding-for-byte-buffers` | Simple Base64 Encoding for Byte Buffers | C 001–053 |
| Completed | `russian-055-polite-phone-and-message-etiquette` | Message and Communication Etiquette | Russian 001–054 |
| Completed | `c-055-simple-base64-decoding-with-validation` | Simple Base64 Decoding with Validation | C 001–054 |
| Completed | `russian-056-idiomatic-expressions-and-sayings` | Common Idiomatic Expressions and Sayings | Russian 001–055 |
| Completed | `c-056-binary-safe-merge-of-two-sorted-byte-lists` | Binary Safe Merge of Two Sorted Byte Lists | C 001–055 |
| Completed | `russian-057-comprehensive-review-and-capstone-dialogue` | Comprehensive Review and Capstone Dialogue | Russian 001–056 |
| Completed | `c-057-comprehensive-byte-stream-state-machine` | Comprehensive Byte-Stream State Machine | C 001–056 |
| Completed | `russian-058-expressing-wishes-and-future-ambitions` | Wishes and Future Ambitions with `хотелось бы` | Russian 001–057 |
| Completed | `c-058-multi-packet-reassembly-buffers` | Multi-Packet Reassembly Buffers | C 001–057 |
| Completed | `russian-059-genitive-possession-and-absence` | Possession and Absence with the Genitive | Russian 001–058 |
| Completed | `c-059-checked-sequence-number-tracking` | Checked Sequence Number Tracking | C 001–058 |
| Completed | `russian-060-numbers-eleven-to-twenty` | Numbers Eleven to Twenty and Counted Nouns | Russian 001–059 |
| Completed | `c-060-owned-dynamic-byte-buffer-lifetime` | Owned Dynamic Byte Buffer Lifetime | C 001–059 |
| Completed | `russian-061-dative-for-recipients-and-needs` | The Dative for Recipients and Needs | Russian 001–060 |
| Completed | `c-061-memmove-and-overlapping-byte-ranges` | Overlapping Byte Ranges with `memmove` | C 001–060 |
| Completed | `russian-062-instrumental-with-companions-and-roles` | Instrumental Case with Companions and Roles | Russian 001–061 |
| Completed | `c-062-sorted-byte-record-search` | Searching Sorted Byte Records | C 001–061 |
| Completed | `russian-063-prepositional-about-topics` | Talking About Topics with `о` | Russian 001–062 |
| Completed | `c-063-versioned-binary-header-validation` | Versioned Binary Header Validation | C 001–062 |
| Completed | `russian-064-verb-aspect-introduction` | An Introduction to Verb Aspect | Russian 001–063 |
| Completed | `c-064-endianness-dispatch-for-word-fields` | Endian Dispatch for Word Fields | C 001–063 |
| Completed | `russian-065-habitual-and-one-time-actions` | Habitual and One-Time Actions | Russian 001–064 |
| Completed | `c-065-calloc-count-size-overflow-checks` | Checked `calloc` Count and Size Arithmetic | C 001–064 |
| Completed | `russian-066-past-tense-aspect-contrast` | Past Tense: Process and Completed Result | Russian 001–065 |
| Completed | `c-066-checked-file-offset-and-size-queries` | Checked File Offset and Size Queries | C 001–065 |
| Completed | `russian-067-future-with-aspect` | Future Meaning and Aspect | Russian 001–066 |
| Completed | `c-067-write-then-verify-binary-records` | Write Then Verify Binary Records | C 001–066 |
| Completed | `russian-068-motion-verbs-idti-and-hodit` | Motion Verbs: `идти` and `ходить` | Russian 001–067 |
| Completed | `c-068-named-status-codes-for-parsers` | Named Status Codes for Parsers | C 001–067 |
| Completed | `russian-069-motion-verbs-ekhat-and-ezdit` | Motion Verbs: `ехать` and `ездить` | Russian 001–068 |
| Completed | `c-069-safe-size-t-multiplication` | Safe `size_t` Multiplication | C 001–068 |
| Completed | `russian-070-prefixed-motion-verbs` | Prefixed Motion Verbs: Arrival and Departure | Russian 001–069 |
| Completed | `c-070-binary-reader-api-with-cursor` | Binary Reader API with a Cursor | C 001–069 |
| Completed | `russian-071-comparative-adjectives-luchshe-huzhe` | Comparisons with `лучше` and `хуже` | Russian 001–070 |
| Completed | `c-071-binary-writer-api-with-cursor` | Binary Writer API with a Cursor | C 001–070 |
| Completed | `russian-072-superlatives-with-samyi` | Superlatives with `самый` | Russian 001–071 |
| Completed | `c-072-magic-and-version-negotiation` | Magic and Version Negotiation | C 001–071 |
| Completed | `russian-073-conditional-with-by` | The Conditional with `бы` | Russian 001–072 |
| Completed | `c-073-streaming-crc32-integrity-check` | Streaming CRC-32 Integrity Check | C 001–072 |
| Completed | `russian-074-relative-clauses-with-kotoryi` | Relative Clauses with `который` | Russian 001–073 |
| Completed | `c-074-fnv1a-toy-hash-and-test-vector` | FNV-1a Educational Hash and Test Vector | C 001–073 |
| Completed | `russian-075-indirect-questions-with-li` | Indirect Questions with `ли` | Russian 001–074 |
| Completed | `c-075-toy-hash-avalanche-observation` | Toy Hash Avalanche Observation | C 001–074 |
| Completed | `russian-076-impersonal-expressions-mozhno-nuzhno` | Impersonal Expressions: `можно`, `нужно`, and `нельзя` | Russian 001–075 |
| Completed | `c-076-established-library-boundary-design` | Established Library Boundary Design | C 001–075 |
| Completed | `russian-077-quantity-words-mnogo-malo` | Quantity Words: `много`, `мало`, and `несколько` | Russian 001–076 |
| Completed | `c-077-byte-format-threat-model-notes` | Byte Format Threat Model Notes | C 001–076 |
| Completed | `russian-078-ordinal-numbers-and-dates` | Ordinal Numbers and Calendar Dates | Russian 001–077 |
| Completed | `c-078-constant-time-comparison-review` | Constant-Time Comparison Review | C 001–077 |
| Completed | `russian-079-making-appointments-and-schedules` | Making Appointments and Schedules | Russian 001–078 |
| Completed | `c-079-parser-fuzz-seed-corpus` | Parser Fuzz Seed Corpus | C 001–078 |
| Completed | `russian-080-asking-and-giving-directions-review` | Asking for and Giving Directions | Russian 001–079 |
| Completed | `c-080-round-trip-property-checks` | Round-Trip Property Checks | C 001–079 |
| Completed | `russian-081-health-and-pharmacy-basics` | Health and Pharmacy Basics | Russian 001–080 |
| Completed | `c-081-sanitizer-oriented-boundary-tests` | Sanitizer-Oriented Boundary Tests | C 001–080 |
| Completed | `russian-082-giving-gentle-advice` | Giving Gentle Advice with `стоит` | Russian 001–081 |
| Completed | `c-082-static-analysis-oriented-error-paths` | Static Analysis Error Paths | C 001–081 |
| Completed | `russian-083-feelings-and-reactions` | Feelings and Reactions | Russian 001–082 |
| Completed | `c-083-portable-file-hygiene-and-cleanup` | Portable File Hygiene and Cleanup | C 001–082 |
| Completed | `russian-084-invitations-and-responses` | Invitations and Responses | Russian 001–083 |
| Completed | `c-084-safe-hex-rendering-for-byte-buffers` | Safe Hex Rendering for Byte Buffers | C 001–083 |
| Completed | `russian-085-formal-requests-and-polite-register` | Formal Requests and Polite Register | Russian 001–084 |
| Completed | `c-085-text-encoding-and-byte-boundaries-review` | Text Encoding and Byte Boundaries Review | C 001–084 |
| Completed | `russian-086-short-messages-and-rsvp` | Short Messages and RSVP | Russian 001–085 |
| Completed | `c-086-checksums-versus-authentication` | Checksums Versus Authentication | C 001–085 |
| Completed | `russian-087-service-encounters-review` | Service Encounters Review | Russian 001–086 |
| Completed | `c-087-cryptographic-library-selection-checklist` | Cryptographic Library Selection Checklist | C 001–086 |
| Completed | `russian-088-workplace-introductions-and-meetings` | Workplace Introductions and Meetings | Russian 001–087 |
| Completed | `c-088-key-material-interface-restrictions` | Key Material Interface Restrictions | C 001–087 |
| Completed | `russian-089-discussing-learning-progress` | Discussing Learning Progress | Russian 001–088 |
| Completed | `c-089-nested-frame-parser-limits` | Nested Frame Parser Limits | C 001–088 |
| Completed | `russian-090-reading-simple-notices-and-forms` | Reading Simple Notices and Forms | Russian 001–089 |
| Completed | `c-090-replay-sequence-window` | Replay Sequence Window | C 001–089 |
| Completed | `russian-091-media-and-news-routines` | Media and News Routines | Russian 001–090 |
| Completed | `c-091-input-limit-configuration` | Input Limit Configuration | C 001–090 |
| Completed | `russian-092-returns-and-exchanges-in-shops` | Returns and Exchanges in Shops | Russian 001–091 |
| Completed | `c-092-sensitive-byte-lifecycle-notes` | Sensitive Byte Lifecycle Notes | C 001–091 |
| Completed | `russian-093-uncertainty-and-probability` | Uncertainty and Probability | Russian 001–092 |
| Completed | `c-093-recoverable-parser-state-machine` | Recoverable Parser State Machine | C 001–092 |
| Completed | `russian-094-conditions-with-esli` | Conditions with `если` | Russian 001–093 |
| Completed | `c-094-malformed-input-regression-corpus` | Malformed Input Regression Corpus | C 001–093 |
| Completed | `russian-095-contrast-with-khotya` | Contrast with `хотя` | Russian 001–094 |
| Completed | `c-095-cross-platform-serialization-contracts` | Cross-Platform Serialization Contracts | C 001–094 |
| Completed | `russian-096-cohesive-paragraph-connectors` | Cohesive Paragraph Connectors | Russian 001–095 |
| Completed | `c-096-record-reader-writer-round-trip` | Record Reader and Writer Round Trip | C 001–095 |
| Completed | `russian-097-narrating-a-simple-trip` | Narrating a Simple Trip | Russian 001–096 |
| Completed | `c-097-binary-log-inspection-format` | Binary Log Inspection Format | C 001–096 |
| Completed | `russian-098-planning-a-small-project` | Planning a Small Project | Russian 001–097 |
| Completed | `c-098-text-and-byte-boundary-capstone` | Text and Byte Boundary Capstone | C 001–097 |
| Completed | `russian-099-opinions-with-evidence` | Opinions with Reasons and Evidence | Russian 001–098 |
| Completed | `c-099-defensive-code-review-checklist` | Defensive Code Review Checklist | C 001–098 |
| Completed | `russian-100-integrated-grammar-review` | Integrated Grammar Review | Russian 001–099 |
| Completed | `c-100-production-crypto-library-integration-notes` | Production Crypto Library Integration Notes | C 001–099 |
| Completed | `russian-101-capstone-dialogue-and-study-log` | Capstone Dialogue and Study Log | Russian 001–100 |
| Completed | `c-101-byte-protocol-validation-capstone` | Byte Protocol Validation Capstone | C 001–100 |
| Completed | `russian-102-reflexive-verbs-for-daily-actions` | Reflexive Verbs for Daily Actions | Russian 001–101 |
| Completed | `c-102-cursor-and-span-api-separation` | Cursor and Span API Separation | C 001–101 |
| Completed | `russian-103-reciprocal-actions-and-each-other` | Reciprocal Actions and `друг друга` | Russian 001–102 |
| Completed | `c-103-explicit-endianness-test-vectors` | Explicit Endianness Test Vectors | C 001–102 |
| Completed | `russian-104-present-tense-conjugation-review` | Present-Tense Conjugation Review | Russian 001–103 |
| Completed | `c-104-checked-integer-narrowing` | Checked Integer Narrowing | C 001–103 |
| Completed | `russian-105-animate-accusative-objects` | Animate Accusative Objects | Russian 001–104 |
| Completed | `c-105-signed-to-unsigned-conversions` | Signed-to-Unsigned Conversion Boundaries | C 001–104 |
| Completed | `russian-106-genitive-after-negation` | Genitive Patterns after Negation | Russian 001–105 |
| Completed | `c-106-boolean-and-status-contracts` | Boolean and Status Contracts | C 001–105 |
| Completed | `russian-107-quantities-and-genitive-phrases` | Quantities and Genitive Phrases | Russian 001–106 |
| Completed | `c-107-bounded-growth-policy` | Bounded Growth Policy | C 001–106 |
| Completed | `russian-108-dative-age-and-impersonal-needs` | Dative for Age and Impersonal Needs | Russian 001–107 |
| Completed | `c-108-object-representation-vs-wire-format` | Object Representation versus Wire Format | C 001–107 |
| Completed | `russian-109-dative-liking-and-help` | Dative for Liking and Help | Russian 001–108 |
| Completed | `c-109-portable-integer-formatting` | Portable Integer Formatting | C 001–108 |
| Completed | `russian-110-instrumental-for-means-and-roles` | Instrumental for Means and Roles | Russian 001–109 |
| Completed | `c-110-aliasing-and-memcpy-boundary` | Aliasing and the `memcpy` Boundary | C 001–109 |
| Completed | `russian-111-prepositional-places-and-topics-review` | Prepositional Places and Topics Review | Russian 001–110 |
| Completed | `c-111-fixed-record-comparator-contract` | Fixed Record Comparator Contract | C 001–110 |
| Completed | `russian-112-case-choice-in-everyday-scenes` | Case Choice in Everyday Scenes | Russian 001–111 |
| Completed | `c-112-sorted-index-validation` | Sorted Index Validation | C 001–111 |
| Completed | `russian-113-aspect-pairs-and-completion` | Aspect Pairs and Completion | Russian 001–112 |
| Completed | `c-113-sequence-gap-detection` | Sequence Gap Detection | C 001–112 |
| Completed | `russian-114-aspect-with-time-adverbs` | Aspect with Time Adverbs | Russian 001–113 |
| Completed | `c-114-bounded-message-assembly` | Bounded Message Assembly | C 001–113 |
| Completed | `russian-115-polite-imperatives-and-safety` | Polite Imperatives and Safe Instructions | Russian 001–114 |
| Completed | `c-115-trailing-data-policy` | Trailing Data Policy | C 001–114 |
| Completed | `russian-116-negative-imperatives-and-caution` | Negative Imperatives and Caution | Russian 001–115 |
| Completed | `c-116-version-migration-dispatch` | Version Migration Dispatch | C 001–115 |
| Completed | `russian-117-motion-verbs-for-round-trips` | Motion Verbs for Round Trips | Russian 001–116 |
| Completed | `c-117-unknown-field-skipping` | Unknown Field Skipping | C 001–116 |
| Completed | `russian-118-motion-prefixes-and-return` | Motion Prefixes and Return | Russian 001–117 |
| Completed | `c-118-duplicate-field-rejection` | Duplicate Field Rejection | C 001–117 |
| Completed | `russian-119-routes-directions-and-transport` | Routes, Directions, and Transport | Russian 001–118 |
| Completed | `c-119-required-field-completeness` | Required Field Completeness | C 001–118 |
| Completed | `russian-120-motion-and-aspect-review` | Motion and Aspect Review | Russian 001–119 |
| Completed | `c-120-canonical-byte-serialization` | Canonical Byte Serialization | C 001–119 |
| Completed | `russian-121-reported-speech-basics` | Reported Speech Basics | Russian 001–120 |
| Completed | `c-121-overflow-safe-binary-search` | Overflow-Safe Binary Search | C 001–120 |
| Completed | `russian-122-past-narration-sequence` | Past Narration and Sequence | Russian 001–121 |
| Completed | `c-122-fixed-capacity-priority-selection` | Fixed-Capacity Priority Selection | C 001–121 |
| Completed | `russian-123-describing-people-with-clauses` | Describing People with Simple Clauses | Russian 001–122 |
| Completed | `c-123-iteration-count-invariants` | Iteration Count Invariants | C 001–122 |
| Completed | `russian-124-relative-pronoun-agreement` | Relative Pronoun Agreement | Russian 001–123 |
| Completed | `c-124-ownership-transfer-contracts` | Ownership Transfer Contracts | C 001–123 |
| Completed | `russian-125-indirect-questions-and-uncertainty` | Indirect Questions and Uncertainty | Russian 001–124 |
| Completed | `c-125-opaque-handle-interface-boundaries` | Opaque Handle Interface Boundaries | C 001–124 |
| Completed | `russian-126-neutral-word-order-and-focus` | Neutral Word Order and Focus | Russian 001–125 |
| Completed | `c-126-callback-error-propagation` | Callback Error Propagation | C 001–125 |
| Completed | `russian-127-connectives-for-reasons-and-contrast` | Connectives for Reasons and Contrast | Russian 001–126 |
| Completed | `c-127-output-parameter-stability` | Output Parameter Stability | C 001–126 |
| Completed | `russian-128-expressing-purpose` | Expressing Purpose | Russian 001–127 |
| Completed | `c-128-cleanup-ladder-pattern` | Cleanup Ladder Pattern | C 001–127 |
| Completed | `russian-129-comparisons-and-evaluations` | Comparisons and Careful Evaluations | Russian 001–128 |
| Completed | `c-129-feature-flag-format-gating` | Feature Flag Format Gating | C 001–128 |
| Completed | `russian-130-approximate-quantities` | Approximate Quantities | Russian 001–129 |
| Completed | `c-130-table-driven-test-cases` | Table-Driven Test Cases | C 001–129 |
| Completed | `russian-131-dates-deadlines-and-plans` | Dates, Deadlines, and Plans | Russian 001–130 |
| Completed | `c-131-error-code-coverage` | Error Code Coverage | C 001–130 |
| Completed | `russian-132-appointments-and-rescheduling` | Appointments and Rescheduling | Russian 001–131 |
| Completed | `c-132-component-contract-documentation` | Component Contract Documentation | C 001–131 |
| Completed | `russian-133-letters-and-email-register` | Letters and Email Register | Russian 001–132 |
| Completed | `c-133-span-interface-consistency` | Span Interface Consistency | C 001–132 |
| Completed | `russian-134-phone-clarification-and-repair` | Phone Clarification and Repair | Russian 001–133 |
| Completed | `c-134-slice-advance-validation` | Slice Advance Validation | C 001–133 |
| Completed | `russian-135-workplace-status-updates` | Workplace Status Updates | Russian 001–134 |
| Completed | `c-135-nested-subrange-checks` | Nested Subrange Checks | C 001–134 |
| Completed | `russian-136-professional-requests` | Professional Requests | Russian 001–135 |
| Completed | `c-136-delimiter-without-c-string-assumption` | Delimiter Search without a C-String Assumption | C 001–135 |
| Completed | `russian-137-constructive-feedback` | Constructive Feedback | Russian 001–136 |
| Completed | `c-137-escaped-text-byte-parser` | Escaped Text Byte Parser | C 001–136 |
| Completed | `russian-138-opinions-with-evidence-review` | Opinions with Evidence Review | Russian 001–137 |
| Completed | `c-138-hex-encode-round-trip` | Hex Encoding Round Trip | C 001–137 |
| Completed | `russian-139-agreeing-and-disagreeing-politely` | Agreeing and Disagreeing Politely | Russian 001–138 |
| Completed | `c-139-strict-hex-decode-round-trip` | Strict Hex Decoding Round Trip | C 001–138 |
| Completed | `russian-140-attribution-and-source-language` | Attribution and Source Language | Russian 001–139 |
| Completed | `c-140-base64-boundary-review` | Base64 Boundary Review | C 001–139 |
| Completed | `russian-141-summarizing-short-texts` | Summarizing Short Texts | Russian 001–140 |
| Completed | `c-141-utf8-structural-byte-validation` | UTF-8 Structural Byte Validation | C 001–140 |
| Completed | `russian-142-describing-processes` | Describing a Simple Process | Russian 001–141 |
| Completed | `c-142-checked-output-encoding-length` | Checked Output Encoding Length | C 001–141 |
| Completed | `russian-143-instructions-and-sequences` | Instructions and Sequences | Russian 001–142 |
| Completed | `c-143-chunked-encoder-state` | Chunked Encoder State | C 001–142 |
| Completed | `russian-144-problem-and-solution-language` | Problem and Solution Language | Russian 001–143 |
| Completed | `c-144-streaming-decoder-state` | Streaming Decoder State | C 001–143 |
| Completed | `russian-145-conditional-planning` | Conditional Planning | Russian 001–144 |
| Completed | `c-145-checked-append-only-journal` | Checked Append-Only Journal | C 001–144 |
| Completed | `russian-146-hypothetical-advice` | Hypothetical Advice | Russian 001–145 |
| Completed | `c-146-journal-recovery-boundaries` | Journal Recovery Boundaries | C 001–145 |
| Completed | `russian-147-goals-and-progress-reflection` | Goals and Progress Reflection | Russian 001–146 |
| Completed | `c-147-atomic-replace-design` | Atomic Replace Design | C 001–146 |
| Completed | `russian-148-learning-strategy-discussion` | Learning Strategy Discussion | Russian 001–147 |
| Completed | `c-148-temporary-file-cleanup` | Temporary File Cleanup | C 001–147 |
| Completed | `russian-149-cultural-events-and-invitations` | Cultural Events and Invitations | Russian 001–148 |
| Completed | `c-149-file-metadata-size-limits` | File Metadata Size Limits | C 001–148 |
| Completed | `russian-150-travel-problem-scenarios` | Travel Problem Scenarios | Russian 001–149 |
| Completed | `c-150-safe-file-name-policy` | Safe File Name Policy | C 001–149 |
| Completed | `russian-151-accommodation-and-complaints` | Accommodation and Polite Complaints | Russian 001–150 |
| Completed | `c-151-deterministic-clock-injection` | Deterministic Clock Injection | C 001–150 |
| Completed | `russian-152-shopping-return-negotiation` | Returns and Exchange Negotiation | Russian 001–151 |
| Completed | `c-152-simulated-timeout-budget` | Simulated Timeout Budget | C 001–151 |
| Completed | `russian-153-service-recovery-dialogue` | Service Recovery Dialogue | Russian 001–152 |
| Completed | `c-153-deterministic-test-input-generator` | Deterministic Test Input Generator | C 001–152 |
| Completed | `russian-154-health-appointment-language` | Health Appointment Language | Russian 001–153 |
| Completed | `c-154-pseudo-random-test-seeds` | Pseudo-Random Test Seeds | C 001–153 |
| Completed | `russian-155-emergency-language-boundaries` | Emergency Language and Boundaries | Russian 001–154 |
| Completed | `c-155-fuzz-harness-entry-contract` | Fuzz Harness Entry Contract | C 001–154 |
| Completed | `russian-156-public-notices-and-rules` | Public Notices and Rules | Russian 001–155 |
| Completed | `c-156-mutation-corpus-safety` | Mutation Corpus Safety | C 001–155 |
| Completed | `russian-157-forms-and-personal-details` | Forms and Personal Details | Russian 001–156 |
| Completed | `c-157-differential-parser-test-shape` | Differential Parser Test Shape | C 001–156 |
| Completed | `russian-158-digital-privacy-phrases` | Digital Privacy Phrases | Russian 001–157 |
| Completed | `c-158-property-test-invariants` | Property Test Invariants | C 001–157 |
| Completed | `russian-159-misinformation-and-uncertainty` | Misinformation and Uncertainty | Russian 001–158 |
| Completed | `c-159-metamorphic-byte-transform-tests` | Metamorphic Byte Transform Tests | C 001–158 |
| Completed | `russian-160-media-summary-and-tone` | Media Summary and Tone | Russian 001–159 |
| Completed | `c-160-minimal-repro-case-format` | Minimal Reproduction Case Format | C 001–159 |
| Completed | `russian-161-environmental-routines` | Environmental Routines | Russian 001–160 |
| Completed | `c-161-integer-boundary-test-matrix` | Integer Boundary Test Matrix | C 001–160 |
| Completed | `russian-162-city-services-and-inquiries` | City Services and Inquiries | Russian 001–161 |
| Completed | `c-162-lifetime-and-alias-test-cases` | Lifetime and Alias Test Cases | C 001–161 |
| Completed | `russian-163-community-event-planning` | Community Event Planning | Russian 001–162 |
| Completed | `c-163-ownership-map-review` | Ownership Map Review | C 001–162 |
| Completed | `russian-164-volunteering-and-polite-boundaries` | Volunteering and Polite Boundaries | Russian 001–163 |
| Completed | `c-164-source-review-preconditions` | Source Review Preconditions | C 001–163 |
| Completed | `russian-165-story-setting-and-background` | Story Setting and Background | Russian 001–164 |
| Completed | `c-165-misuse-resistant-api-design` | Misuse-Resistant API Design | C 001–164 |
| Completed | `russian-166-narrative-sequencing` | Narrative Sequencing | Russian 001–165 |
| Completed | `c-166-token-budget-parser-limit` | Token Budget Parser Limit | C 001–165 |
| Completed | `russian-167-character-motivation` | Character Motivation | Russian 001–166 |
| Completed | `c-167-work-quota-accounting` | Work Quota Accounting | C 001–166 |
| Completed | `russian-168-speech-and-thought-in-stories` | Speech and Thought in Stories | Russian 001–167 |
| Completed | `c-168-nesting-depth-caps` | Nesting Depth Caps | C 001–167 |
| Completed | `russian-169-contrast-and-expectation` | Contrast and Expectation | Russian 001–168 |
| Completed | `c-169-message-count-limits` | Message Count Limits | C 001–168 |
| Completed | `russian-170-nuanced-certainty` | Nuanced Certainty | Russian 001–169 |
| Completed | `c-170-bounded-work-per-input` | Bounded Work per Input | C 001–169 |
| Completed | `russian-171-expressing-regret` | Expressing Regret | Russian 001–170 |
| Completed | `c-171-safe-untrusted-input-logging` | Safe Untrusted Input Logging | C 001–170 |
| Completed | `russian-172-offering-support` | Offering Support | Russian 001–171 |
| Completed | `c-172-error-report-redaction` | Error Report Redaction | C 001–171 |
| Completed | `russian-173-negotiating-plans` | Negotiating Plans | Russian 001–172 |
| Completed | `c-173-truncated-hex-diagnostics` | Truncated Hex Diagnostics | C 001–172 |
| Completed | `russian-174-project-brief-language` | Project Brief Language | Russian 001–173 |
| Completed | `c-174-diagnostic-code-taxonomy` | Diagnostic Code Taxonomy | C 001–173 |
| Completed | `russian-175-project-checkin-language` | Project Check-In Language | Russian 001–174 |
| Completed | `c-175-grammar-driven-format-contract` | Grammar-Driven Format Contract | C 001–174 |
| Completed | `russian-176-reviewing-clarity` | Reviewing Clarity | Russian 001–175 |
| Completed | `c-176-table-driven-parser-state-machine` | Table-Driven Parser State Machine | C 001–175 |
| Completed | `russian-177-presenting-a-small-idea` | Presenting a Small Idea | Russian 001–176 |
| Completed | `c-177-composable-bounded-parser-stages` | Composable Bounded Parser Stages | C 001–176 |
| Completed | `russian-178-questions-and-answers` | Questions and Answers | Russian 001–177 |
| Completed | `c-178-tagged-field-wire-types` | Tagged Field Wire Types | C 001–177 |
| Completed | `russian-179-meeting-facilitation` | Meeting Facilitation | Russian 001–178 |
| Completed | `c-179-extensible-tlv-field-rules` | Extensible TLV Field Rules | C 001–178 |
| Completed | `russian-180-meeting-follow-up` | Meeting Follow-Up | Russian 001–179 |
| Completed | `c-180-tagged-union-validation` | Tagged Union Validation | C 001–179 |
| Completed | `russian-181-study-debate` | Study Debate | Russian 001–180 |
| Completed | `c-181-tagged-union-regression-tests` | Tagged Union Regression Tests | C 001–180 |
| Completed | `russian-182-evidence-qualifiers` | Evidence Qualifiers | Russian 001–181 |
| Completed | `c-182-compatibility-matrix-tests` | Compatibility Matrix Tests | C 001–181 |
| Completed | `russian-183-hypotheses-and-limits` | Hypotheses and Limits | Russian 001–182 |
| Completed | `c-183-canonical-input-rejection` | Canonical Input Rejection | C 001–182 |
| Completed | `russian-184-basic-data-description` | Basic Data Description | Russian 001–183 |
| Completed | `c-184-multi-record-file-envelope` | Multi-Record File Envelope | C 001–183 |
| Completed | `russian-185-process-comparison` | Process Comparison | Russian 001–184 |
| Completed | `c-185-checked-index-offsets` | Checked Index Offsets | C 001–184 |
| Completed | `russian-186-risk-and-mitigation-language` | Risk and Mitigation Language | Russian 001–185 |
| Completed | `c-186-metadata-checksum-limits` | Metadata Checksum Limits | C 001–185 |
| Completed | `russian-187-ethical-disagreement` | Ethical Disagreement | Russian 001–186 |
| Completed | `c-187-authenticated-envelope-architecture` | Authenticated Envelope Architecture | C 001–186 |
| Completed | `russian-188-formal-complaint-structure` | Formal Complaint Structure | Russian 001–187 |
| Completed | `c-188-library-wrapper-interface` | Library Wrapper Interface | C 001–187 |
| Completed | `russian-189-formal-response-structure` | Formal Response Structure | Russian 001–188 |
| Completed | `c-189-third-party-error-mapping` | Third-Party Error Mapping | C 001–188 |
| Completed | `russian-190-collaborative-writing` | Collaborative Writing | Russian 001–189 |
| Completed | `c-190-secure-default-configuration` | Secure Default Configuration | C 001–189 |
| Completed | `russian-191-revision-and-editing` | Revision and Editing | Russian 001–190 |
| Completed | `c-191-no-custom-crypto-policy` | No-Custom-Crypto Policy | C 001–190 |
| Completed | `russian-192-reading-longer-texts` | Reading Longer Texts Strategically | Russian 001–191 |
| Completed | `c-192-protocol-documentation-checklist` | Protocol Documentation Checklist | C 001–191 |
| Completed | `russian-193-listening-strategy-language` | Listening Strategy Language | Russian 001–192 |
| Completed | `c-193-release-readiness-checklist` | Release Readiness Checklist | C 001–192 |
| Completed | `russian-194-presentation-practice` | Presentation Practice | Russian 001–193 |
| Completed | `c-194-test-vector-packaging` | Test Vector Packaging | C 001–193 |
| Completed | `russian-195-advanced-case-review` | Advanced Case Review | Russian 001–194 |
| Completed | `c-195-continuous-integration-command-set` | Continuous Integration Command Set | C 001–194 |
| Completed | `russian-196-aspect-and-motion-review` | Aspect and Motion Review | Russian 001–195 |
| Completed | `c-196-reproducible-build-metadata` | Reproducible Build Metadata | C 001–195 |
| Completed | `russian-197-register-review` | Register Review | Russian 001–196 |
| Completed | `c-197-portability-configuration-boundaries` | Portability Configuration Boundaries | C 001–196 |
| Completed | `russian-198-integrated-service-project` | Integrated Service Project Dialogue | Russian 001–197 |
| Completed | `c-198-defensive-parser-library-capstone` | Defensive Parser Library Capstone | C 001–197 |
| Completed | `russian-199-integrated-workshop-discussion` | Integrated Workshop Discussion | Russian 001–198 |
| Completed | `c-199-bounded-writer-library-capstone` | Bounded Writer Library Capstone | C 001–198 |
| Completed | `russian-200-exam-style-self-assessment` | Exam-Style Self-Assessment | Russian 001–199 |
| Completed | `c-200-conformance-test-capstone` | Conformance Test Capstone | C 001–199 |
| Completed | `russian-201-final-portfolio-dialogue-and-reflection` | Final Portfolio Dialogue and Reflection | Russian 001–200 |
| Completed | `c-201-final-byte-systems-review` | Final Byte Systems Review | C 001–200 |

## Long-range C milestones

| Stage | Topics |
|---|---|
| Foundations | Build process, types, control flow, functions, arrays, strings, pointers, lifetime, and errors |
| File handling | Introduced no later than lesson 10: text/binary modes, checked I/O, bounded chunk processing, partial reads/writes, and I/O errors |
| Byte-oriented programming | `uint8_t`, `uint32_t`, byte buffers versus C strings, endianness, shifts, masks, and unsigned arithmetic |
| Secure implementation practice | Bounds checks, allocation-size checks, overflow/truncation prevention, tests, sanitizers, fuzzing concepts, and review |
| Hashing and cryptography literacy | Threat models, checksums versus cryptographic hashes, test vectors, and safe use of established libraries |

> Educational cryptographic examples must be labeled insecure and must never be presented as suitable for protecting real data. The curriculum excludes shellcode, native-code loaders, arbitrary assembly execution, process injection, and self-modifying code.

## Review and adaptation record

- **2026-09-18 — Russian 001 completed.** No earlier lesson was available for retrieval practice, so the lesson established a cover-and-recall routine using a phrase table. It introduced Cyrillic as a sound-based writing system, highlighted high-risk Latin look-alikes, and practised formal versus informal greetings plus a short self-introduction. No learner feedback was available. The next topic is C 001: program structure, compilation, fundamental types, and output.
- **2026-09-19 — C 001 completed.** The lesson briefly recalled the preceding Russian material, then introduced the hosted `main` function, `#include <stdio.h>`, strict C17 compilation, `char`, `int`, `double`, `void`, and type-matched `printf` conversions. The companion program checks output errors, and its expected output was verified by a deterministic shell test plus AddressSanitizer and UndefinedBehaviorSanitizer builds. No learner feedback was available. The next topic is Russian 002: high-value Cyrillic sound patterns, stress awareness, and fuller introductions.
- **2026-09-19 — Russian 002 completed.** The lesson retrieved Russian greetings and the prior C format-specifier concept, then introduced word stress, vowel reduction awareness, the soft sign, high-value consonants, and formal/informal origin questions. It added a short formal self-introduction plus a spaced-repetition plan. No learner feedback was available. The next topic is C 002: expressions, decisions, loops, and a first checked input boundary.
- **2026-09-20 — C 002 completed.** The lesson retrieved core C output and Russian introduction material, then introduced arithmetic and comparison expressions, `if`/`else`, bounded `for` loops, and a checked line-input boundary using `fgets`, `strtol`, `errno`, an end pointer, trailing-text checks, and a pre-cast application range. Its companion program rejects missing, malformed, out-of-range, and overlong input. Strict compilation, exact-output tests, long-line rejection, and AddressSanitizer/UndefinedBehaviorSanitizer runs all passed. No learner feedback was available. The next topic is Russian 003: grammatical gender, basic noun patterns, and familiar objects.
- **2026-09-20 — Russian 003 completed.** The lesson retrieved earlier Russian and C material, introduced the three grammatical genders through dictionary-form endings, highlighted soft-sign ambiguity and common exceptions, and paired everyday objects with `он`, `она`, and `оно`. It included a short object-identification dialogue and a gender-package recall routine. No learner feedback was available. The next topic is C 003: function boundaries, parameters, return values, and explicit error propagation.
- **2026-09-21 — C 003 completed.** The lesson retrieved C 002’s checked parsing pattern and Russian 003’s neuter pronoun, then introduced focused function interfaces, named status codes, output parameters, pointer-null checks, precondition checks for multiplication, and explicit error propagation to `main`. The companion program validates two command-line values, checks every helper status, and prints a session plan only on full success. Strict compilation, deterministic boundary-case tests, and AddressSanitizer/UndefinedBehaviorSanitizer runs all passed. No learner feedback was available. The next topic is Russian 004: gender agreement in common adjectives and concise object descriptions.
- **2026-09-21 — Russian 004 completed.** The lesson retrieved noun gender and C status-check concepts, then introduced singular nominative adjective agreement through `новый — новая — новое` and `маленький — маленькая — маленькое`. It paired gender packages with simple object questions and descriptions, plus spaced recall. No learner feedback was available. The next topic is C 004: fixed-size arrays, C string termination, length limits, and index validation.
- **2026-09-22 — C 004 completed.** The lesson retrieved bounded parsing, output-parameter, and Russian adjective concepts, then distinguished fixed-size arrays from null-terminated C strings. It introduced capacity versus logical string length, `size_t` counts, newline/terminator space, oversized-line rejection, and index validation against `strlen` rather than array capacity. The companion program rejects empty and oversized labels plus malformed, negative, and out-of-range indexes. Strict compilation, deterministic boundary-case tests, and AddressSanitizer/UndefinedBehaviorSanitizer runs all passed. No learner feedback was available. The next topic is Russian 005: high-frequency present-tense verbs and short daily-routine statements.
- **2026-09-22 — Russian 005 completed.** The lesson retrieved gender agreement and C string-bound concepts, then introduced the infinitives and present-tense forms of `работать`, `читать`, and `учиться`. It added three routine time words, informal and polite question models, and a short three-part routine for spoken production. No learner feedback was available. The next topic is C 005: array parameters, pointer relationships, `size_t` counts, and bounded traversal.
- **2026-09-23 — C 005 completed.** The lesson retrieved array and string boundary concepts plus a Russian routine form, then introduced the pointer-plus-count array-interface contract, array-parameter adjustment, `size_t` counts, and forward bounded traversal. The companion program validates pointers, counts, element values, and each total before adding. Its deterministic report test and helper-contract harness cover valid, null-pointer, zero-count, negative-value, and over-limit-value paths; strict compilation and AddressSanitizer/UndefinedBehaviorSanitizer runs all passed. No learner feedback was available. The next topic is Russian 006: common places, `где?` questions, and basic location phrases.
- **2026-09-23 — Russian 006 completed.** The lesson retrieved present-tense routines and C array-interface concepts, then introduced `Где?` location questions and the memorized location packages `дома`, `в школе`, `в парке`, `в библиотеке`, and `на работе`. It combined those packages with existing routine verbs and set aside motion/destination patterns for a later lesson. No learner feedback was available. The next topic is C 006: `uint8_t`, byte arrays versus strings, explicit lengths, and bounded byte traversal.
- **2026-09-24 — C 006 completed.** The lesson retrieved pointer-plus-count and C-string boundary concepts plus a Russian location phrase, then introduced exact-width `uint8_t` data, explicit byte-buffer lengths, hexadecimal output with `PRIX8`, and bounded byte traversal that preserves embedded zero-valued bytes. Its deterministic report test and helper-contract harness cover valid buffers, null buffers, zero-length input, and null output pointers; strict compilation and AddressSanitizer/UndefinedBehaviorSanitizer runs all passed. No learner feedback was available. The next topic is Russian 007: yes/no questions, `не`, and concise responses about routines and locations.
- **2026-09-24 — Russian 007 completed.** The lesson retrieved location phrases and byte-buffer boundaries, then introduced everyday yes/no questions formed with unchanged statement order and question intonation, plus `не` in basic present-tense and location negation. It practised full `Да, ...` and `Нет, ...` responses using established routine and location vocabulary. No learner feedback was available. The next topic is C 007: `uint32_t`, masks, shifts, and bounded word-oriented data handling.
- **2026-09-25 — C 007 completed.** The lesson retrieved explicit byte-buffer boundaries and Russian negation, then introduced exact-width `uint32_t` words, named 8/8/16-bit field layouts, masks, bounded shifts, range checks, and field extraction. The companion program packs and reports logical metadata words without defining a byte serialization or cryptographic operation. Its deterministic report test and helper-contract harness cover valid packing, value-range rejection, output preservation on rejected input, and null output pointers; strict compilation and AddressSanitizer/UndefinedBehaviorSanitizer runs all passed. No learner feedback was available. The next topic is Russian 008: possessive pronouns, familiar people, and short ownership statements.

- **2026-09-25 — Expanded batch completed (Russian 008–017 and C 008–017).** The Russian track advanced through possession, plurals, inanimate direct objects, time words, singular and plural past forms, simple future, ability, wants, and cumulative controlled practice. The C track advanced through explicit byte order, robust file copying, bounded file statistics, strict parsing, dynamic buffers, safe rotations, an explicitly non-cryptographic toy checksum, dynamic file reading, fixed-header serialization, and fixed-record truncation checks. All ten C companion programs passed strict C17 compilation, deterministic tests, static analysis, and AddressSanitizer/UndefinedBehaviorSanitizer execution. No learner feedback was available. The next topic is Russian 018: directions and general place phrases.

- **2026-09-25 — Expanded batch completed (Russian 018–027 and C 018–027).** The Russian track added directions, controlled destinations, numbers, people vocabulary, weather, simple requests, days, comparisons, politeness, and a cumulative fictional conversation. The C track added checked record writing, append output, counted byte copy and equality, overflow-aware `uint32_t` arithmetic, histograms, length prefixes, frame decoding, streaming processing, and a clearly insecure toy mix with a fixed vector. All ten C examples passed strict C17 compilation, deterministic tests, static analysis, and AddressSanitizer/UndefinedBehaviorSanitizer execution. No learner feedback was available. The next topic is Russian 028: likes, dislikes, and basic preferences.

- **2026-09-25 — Batch expansion completed (Russian 028–057 and C 028–057).** The Russian track advanced from basic preferences through shopping, adverbs of frequency, imperatives, languages, spatial relations, occupations, reasoning, body parts, opinions, weather nuances, messages, sports, household rooms, clothing, travel, cafes, clarifications, future plans, formal etiquette, idioms, and an integrated capstone dialogue. The C track covered structured text parsing, hexadecimal decoding, byte reversing, substring matching, run-length decoding, TLV validation, sliding windows, ring buffers, prefix slices, varint encoding/decoding, parity, popcount, XOR masks, CRC-8 (bit-by-bit and table), deduplication, TLV frames, big-endian 64-bit integer conversions, constant-time equality, toy sponge mixing, FIFO queues, compaction, bitsets, Base64 encode/decode, sorted list merging, and an explicit protocol state machine. All 30 companion C programs passed strict -std=c17 compilation, deterministic shell test assertions, -fanalyzer static analysis, and AddressSanitizer/UndefinedBehaviorSanitizer executions. The next scheduled topic is Russian 058: expressing wishes and future ambitions with `хотелось бы`.

- **2026-09-25 — Batch expansion completed (Russian 058–101 and C 058–101).** The Russian track advanced through possession, numbers, the dative and instrumental cases, topic phrases, aspect, motion verbs, conditionals, relative and indirect questions, impersonal expressions, appointments, service encounters, reading notices, cautious opinions, cohesion, narrative, planning, and an integrated capstone dialogue plus study log. The C track advanced through bounded reassembly, sequence tracking, ownership and allocation arithmetic, record APIs, safe file I/O, parser status handling, explicit endianness, non-cryptographic CRC and toy-hash literacy, library-boundary design, threat modeling, fuzz seeds, round-trip tests, sanitizers, static-analysis-ready error paths, protocol limits, and defensive parsing capstones. All 44 C companion programs passed strict C17 compilation, deterministic tests, GCC static analysis, and AddressSanitizer/UndefinedBehaviorSanitizer execution. The next scheduled topic is Russian 102.

- **2026-09-25 — Extended batch completed (Russian 102–201 and C 102–201).** The Russian track advanced through reflexive and reciprocal verbs, case-pattern review, aspect and motion review, reported and relative structures, polite professional and service communication, source-aware summaries, collaborative discussion, evidence qualifiers, narrative work, revision, and final portfolio reflection. The C track advanced through explicit span and cursor APIs, checked conversions and allocations, portable serialization, bounded parser and writer components, safe file operations, deterministic test design, input limits, safe diagnostics, tagged and versioned formats, and a strict no-custom-cryptography library-boundary policy. All 100 C companion programs passed strict ISO C17 compilation, deterministic tests, GCC static analysis, and AddressSanitizer/UndefinedBehaviorSanitizer execution. The prepared curriculum now ends at lesson 201 on both tracks; no lesson 202 may be created without an explicit future extension or remediation request.
