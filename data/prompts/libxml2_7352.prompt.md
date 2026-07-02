==== BLOCKER ====
Target: libxml2
Branch ID: 7352
Location: /src/libxml2/SAX2.c:787:9
Enclosing function: xmlSAX2ElementDecl
Source line:     if (ctxt->inSubset == 1)
Globally blocked side: T  (true branch)

==== TRIAL VECTOR (per fuzzer, n=10 trials) ====
fuzzer                    resolved  blocked  unreached  role
naive                            0       10          0  REFERENCE
cmplog                           0       10          0  loser (value_profile vs value_profile_cmplog)
value_profile                    4        6          0  REFERENCE
value_profile_cmplog             9        1          0  winner (value_profile vs cmplog)
naive_ctx                        7        3          0  REFERENCE
naive_ngram4                     ?        ?          ?  REFERENCE
mopt                             ?        ?          ?  REFERENCE
minimizer                        1        9          0  REFERENCE
fast                             0       10          0  REFERENCE
grimoire                         1        9          0  REFERENCE

INVOLVED fuzzers (synthetic-verification scope): ['cmplog', 'value_profile_cmplog']
REFERENCE fuzzers (auxiliary context only):     ['naive', 'value_profile', 'naive_ctx', 'naive_ngram4', 'mopt', 'minimizer', 'fast', 'grimoire']

==== DECISIVE PAIRS (1) ====
--- Pair 1: value_profile_cmplog > cmplog  [delta: value_profile] ---
  subject 33  (value_profile_cmplog vs cmplog, admissible)
  winner: resolved=9/10  blocked=1  unreached=0
  loser:  resolved=0/10  blocked=10  unreached=0
  avg duration blocked: winner=13.60h  loser=23.90h
  avg hitcount on branch: winner=4  loser=0
  prob_div=0.90  dur_div=10.30h  hit_div=4
  subject-level: delta_AUC=48643200.0  p_AUC=0.0002  delta_Final=809.5  p_final=0.0002

==== SOURCE CONTEXT (per-role coverage overlay) ====
Legend: [W] winner-resolving only  [L] loser-blocking only  [B] both  [ ] not hit
Source: db/per_role_coverage/libxml2/7352/{W,L}/branch_coverage_show.txt

--- Enclosing function: xmlSAX2ElementDecl (/src/libxml2/SAX2.c:772-807) ---
[ ]   770  xmlSAX2ElementDecl(void *ctx, const xmlChar * name, int type,
[ ]   771              xmlElementContentPtr content)
[B]   772  {
[B]   773      xmlParserCtxtPtr ctxt = (xmlParserCtxtPtr) ctx;
[B]   774      xmlElementPtr elem = NULL;
[ ]   775
[ ]   776      /* Avoid unused variable warning if features are disabled. */
[B]   777      (void) elem;
[ ]   778
[B]   779      if ((ctxt == NULL) || (ctxt->myDoc == NULL))
[ ]   780          return;
[ ]   781
[ ]   782  #ifdef DEBUG_SAX
[ ]   783      xmlGenericError(xmlGenericErrorContext,
[ ]   784                      "SAX.xmlSAX2ElementDecl(%s, %d, ...)\n", name, type);
[ ]   785  #endif
[ ]   786
[B]   787      if (ctxt->inSubset == 1) <-- BLOCKER
[W]   788          elem = xmlAddElementDecl(&ctxt->vctxt, ctxt->myDoc->intSubset,
[W]   789                                   name, (xmlElementTypeVal) type, content);
[L]   790      else if (ctxt->inSubset == 2)
[L]   791          elem = xmlAddElementDecl(&ctxt->vctxt, ctxt->myDoc->extSubset,
[L]   792                                   name, (xmlElementTypeVal) type, content);
[ ]   793      else {
[ ]   794          xmlFatalErrMsg(ctxt, XML_ERR_INTERNAL_ERROR,
[ ]   795  	     "SAX.xmlSAX2ElementDecl(%s) called while not in subset\n",
[ ]   796  	               name, NULL);
[ ]   797          return;
[ ]   798      }
[B]   799  #ifdef LIBXML_VALID_ENABLED
[B]   800      if (elem == NULL)
[ ]   801          ctxt->valid = 0;
[B]   802      if (ctxt->validate && ctxt->wellFormed &&
[B]   803          ctxt->myDoc && ctxt->myDoc->intSubset)
[L]   804          ctxt->valid &=
[L]   805              xmlValidateElementDecl(&ctxt->vctxt, ctxt->myDoc, elem);
[B]   806  #endif /* LIBXML_VALID_ENABLED */
[B]   807  }

--- No 1-hop callers of xmlSAX2ElementDecl fired in W (callers index present but none matched) ---

==== HIT-COUNT DIVERGENCE (per function in cov dump) ====
Functions where W and L invocation counts differ by >=3.0x or one is zero. 'Entry-line count' = first executable line in the function body — a proxy for invocation count.

  W hits    L hits  function  (file:start-end)
       3        41  xmlSAX2ElementDecl  (/src/libxml2/SAX2.c:772-807)  <-- enclosing
       4        40  xmlSAXVersion  (/src/libxml2/SAX2.c:2886-2930)
       0        30  xmlSAX2ExternalSubset  (/src/libxml2/SAX2.c:368-496)
       0        30  xmlSAX2ResolveEntity  (/src/libxml2/SAX2.c:514-538)
       0        29  SAX2.c:xmlSAX2TextNode  (/src/libxml2/SAX2.c:1868-1943)
       3        30  xmlSAX2InternalSubset  (/src/libxml2/SAX2.c:330-354)
       3        30  xmlSAX2SetDocumentLocator  (/src/libxml2/SAX2.c:942-948)
       3        30  xmlSAX2StartDocument  (/src/libxml2/SAX2.c:958-1013)
       0        25  SAX2.c:xmlSAX2Text  (/src/libxml2/SAX2.c:2547-2671)
       0        25  xmlSAX2Characters  (/src/libxml2/SAX2.c:2683-2685)
       0        21  xmlSAX2AttributeDecl  (/src/libxml2/SAX2.c:701-758)
       0        12  xmlSAX2StartElementNs  (/src/libxml2/SAX2.c:2228-2459)
       0         9  xmlSAX2EndDocument  (/src/libxml2/SAX2.c:1023-1054)
       0         9  SAX2.c:xmlCheckDefaultedAttributes  (/src/libxml2/SAX2.c:1447-1591)
       0         9  xmlSAX2StartElement  (/src/libxml2/SAX2.c:1603-1806)
... (7 more divergent functions)

==== DIVERGENT BRANCHES (on call chain, rough order) ====
Branches with W/L divergence in functions on the call chain (enclosing + 1-hop + chain). Rough execution order: outermost caller → blocker. Caveats: this assumes no recursion; loops/gotos break source-line order locally; off-chain divergent branches (L explored code W didn't, or vice versa) are summarized below the chain section — see HIT-COUNT DIVERGENCE for the function-level view.

depth     src  W(T/F)  L(T/F)  source
--- d=1  xmlSAX2ElementDecl  (/src/libxml2/SAX2.c:772-807) ---
  d=1   L 779  T=0 F=3  T=0 F=41  if ((ctxt == NULL) || (ctxt->myDoc == NULL))
  d=1   L 779  T=0 F=3  T=0 F=41  if ((ctxt == NULL) || (ctxt->myDoc == NULL))
  d=1   L 787  T=3 F=0  T=0 F=41  if (ctxt->inSubset == 1)  <-- BLOCKER
  d=1   L 790  T=0 F=0  T=41 F=0  else if (ctxt->inSubset == 2)
  d=1   L 800  T=0 F=3  T=0 F=41  if (elem == NULL)
  d=1   L 802  T=0 F=3  T=5 F=36  if (ctxt->validate && ctxt->wellFormed &&
  d=1   L 802  T=0 F=0  T=5 F=0  if (ctxt->validate && ctxt->wellFormed &&
  d=1   L 803  T=0 F=0  T=5 F=0  ctxt->myDoc && ctxt->myDoc->intSubset)
  d=1   L 803  T=0 F=0  T=5 F=0  ctxt->myDoc && ctxt->myDoc->intSubset)

[off-chain: 234 additional divergent branches across 18 functions (see HIT-COUNT DIVERGENCE for which functions)]

==== BRANCH SEEDS (shared across decisive pairs) ====
Note: seed_bisect picks one (fuzzer, trial) per direction by lex-min, so the storing fuzzer may differ from a pair's decisive winner/loser. Each seed below carries its actual (fuzzer, trial); the bytes exercise the named branch side regardless of provenance.

==== Winner-resolving seeds (take true branch) ====
Seed 1 (id=81e56072f992fc6e, size=280 bytes, fuzzer=value_profile_cmplog, trial=2, discovered_at=81019s, mutation_op=BytesInsertCopyMutator,BytesSwapMutator):
  0000: 25 39 39 39 2f 78 06 00 00 00 31 32 37 37 37 32   %999/x....127772
  0010: 2e 01 00 00 5c 0a 3c 3f 78 6d 6c 20 76 65 72 73   ....\.<?xml vers
  0020: 69 6f 6e 3d 22 31 2e 30 22 3f 3e 0a 3c 21 44 4f   ion="1.0"?>.<!DO
  0030: 43 54 59 50 45 20 61 20 53 59 53 54 45 4d 20 22   CTYPE a SYSTEM "

==== Loser-blocking seeds (take false branch) ====
Seed 1 (id=08a5affb9847d606, size=391 bytes, fuzzer=cmplog, trial=1, discovered_at=57s, mutation_op=BytesInsertCopyMutator,BytesRandInsertMutator,BytesInsertCopyMutator):
  0000: 06 00 00 00 31 32 37 37 37 32 2e 78 6d 6c 5c 0a   ....127772.xml\.
  0010: 3c 3f 78 6d 6c 20 76 65 72 73 69 6f 6e 3d 22 31   <?xml version="1
  0020: 2e 30 22 3f 3e 0a 3c 21 44 4f 43 54 59 50 45 20   .0"?>.<!DOCTYPE
  0030: 61 20 53 59 53 54 45 4d 20 22 64 74 64 73 2f 31   a SYSTEM "dtds/1
Seed 2 (id=037ce516e253ed97, size=556 bytes, fuzzer=cmplog, trial=1, discovered_at=96s, mutation_op=QwordAddMutator,BitFlipMutator,CrossoverInsertMutator,BytesExpandMutator):
  0000: 06 00 00 00 31 32 37 37 37 32 2e 78 6d 6c 5c 0a   ....127772.xml\.
  0010: 3c 3f 78 6d 6c 20 76 65 72 73 69 6f 6e 3d 22 31   <?xml version="1
  0020: 2e 30 22 3f 3e 0a 3c 21 44 4f 43 54 59 50 45 20   .0"?>.<!DOCTYPE
  0030: 61 20 53 59 53 54 45 4d 20 22 64 74 64 73 2f 31   a SYSTEM "dtds/1
Seed 3 (id=01986dbadd87a561, size=368 bytes, fuzzer=cmplog, trial=1, discovered_at=128s, mutation_op=ByteDecMutator,CrossoverReplaceMutator,BytesRandInsertMutator,ByteAddMutator,ByteAddMutator,CrossoverReplaceMutator):
  0000: 06 00 00 00 31 32 37 37 37 32 2e 78 6d 6c 5c 0a   ....127772.xml\.
  0010: 3c 3f 78 6d 6c 20 76 65 72 73 69 6f 6e 3d 22 31   <?xml version="1
  0020: 2e 30 22 3f 3e 0a 3c 21 44 4f 43 54 59 50 45 20   .0"?>.<!DOCTYPE
  0030: 61 20 53 59 53 54 45 4d 20 22 64 74 64 73 2f 31   a SYSTEM "dtds/1
Seed 4 (id=001c8c9d4510710d, size=248 bytes, fuzzer=cmplog, trial=1, discovered_at=411s, mutation_op=ByteNegMutator,BytesCopyMutator,BytesInsertMutator,ByteIncMutator,BytesSwapMutator,BytesDeleteMutator,TokenReplace):
  0000: 5f 5f 5f 00 31 32 37 37 37 32 2e 78 6d 6c 5c 0a   ___.127772.xml\.
  0010: 3c 3f 78 6d 6c 20 76 65 72 73 69 6f 6e 3d 22 31   <?xml version="1
  0020: 2e 30 22 3f 3e 0a 3c 21 44 4f 43 54 59 50 45 20   .0"?>.<!DOCTYPE
  0030: 61 20 53 59 53 54 45 4d 20 22 64 74 64 73 2f 31   a SYSTEM "dtds/1
Seed 5 (id=011f3466d38e335c, size=368 bytes, fuzzer=cmplog, trial=1, discovered_at=575s, mutation_op=TokenInsert):
  0000: 06 00 00 00 31 32 37 37 37 32 2e 78 6d 6c 5c 0a   ....127772.xml\.
  0010: 3c 3f 78 6d 6c 20 76 65 72 73 69 6f 6e 3d 22 31   <?xml version="1
  0020: 2e 30 22 3f 3e 0a 3c 21 44 4f 43 54 59 50 45 20   .0"?>.<!DOCTYPE
  0030: 61 20 53 59 53 54 45 4d 20 22 64 74 64 73 2f 31   a SYSTEM "dtds/1

==== BYTE DIFF (W vs L at common offsets) ====
Per-offset byte sets. Format: hex(ascii)xCOUNT. Shows offsets where W and L bytes differ AND at least one side is concentrated (≤4 distinct values) — likely-informative dataflow signal.

 Offset  W bytes                             L bytes                             tag
   0x0000  25(%)x1                             06(.)x6 5f(_)x1 2b(+)x1 6b(k)x1 +1u  DIFFER
   0x0001  39(9)x1                             00(.)x7 5f(_)x1 27(')x1 66(f)x1     DIFFER
   0x0002  39(9)x1                             00(.)x7 5f(_)x1 0a(.)x1 3d(=)x1     DIFFER
   0x0003  39(9)x1                             00(.)x8 20( )x1 22(")x1             DIFFER
   0x0004  2f(/)x1                             31(1)x7 00(.)x1 20( )x1 67(g)x1     DIFFER
   0x0005  78(x)x1                             32(2)x7 d0(.)x1 20( )x1 74(t)x1     DIFFER
   0x0006  06(.)x1                             37(7)x7 01(.)x1 20( )x1 74(t)x1     DIFFER
   0x0007  00(.)x1                             37(7)x7 00(.)x1 20( )x1 70(p)x1     PARTIAL
   0x0008  00(.)x1                             37(7)x7 31(1)x1 20( )x1 3a(:)x1     DIFFER
   0x0009  00(.)x1                             32(2)x8 20( )x1 2f(/)x1             DIFFER
   0x000a  31(1)x1                             2e(.)x7 37(7)x1 20( )x1 2f(/)x1     DIFFER
   0x000b  32(2)x1                             78(x)x7 37(7)x1 20( )x1 66(f)x1     DIFFER
   0x000c  37(7)x1                             6d(m)x7 37(7)x1 20( )x1 61(a)x1     PARTIAL
   0x000d  37(7)x1                             6c(l)x7 32(2)x1 20( )x1 6b(k)x1     DIFFER
   0x000e  37(7)x1                             5c(\)x7 2d(-)x1 20( )x1 65(e)x1     DIFFER
   0x000f  32(2)x1                             0a(.)x7 00(.)x1 78(x)x1 75(u)x1     DIFFER
   0x0010  2e(.)x1                             3c(<)x7 6d(m)x1 6c(l)x1 10(.)x1     DIFFER
   0x0011  01(.)x1                             3f(?)x7 6c(l)x1 69(i)x1 10(.)x1     DIFFER
   0x0012  00(.)x1                             78(x)x7 5c(\)x1 6e(n)x1 10(.)x1     DIFFER
   0x0013  00(.)x1                             6d(m)x7 0a(.)x1 6b(k)x1 04(.)x1     DIFFER
   0x0014  5c(\)x1                             6c(l)x7 3c(<)x1 3a(:)x1 10(.)x1     DIFFER
   0x0015  0a(.)x1                             20( )x7 3f(?)x1 74(t)x1 21(!)x1     DIFFER
   0x0016  3c(<)x1                             76(v)x7 78(x)x1 79(y)x1 3e(>)x1     DIFFER
   0x0017  3f(?)x1                             65(e)x7 6d(m)x1 70(p)x1 0a(.)x1     DIFFER
   0x0018  78(x)x1                             72(r)x7 6c(l)x1 65(e)x1 0a(.)x1     DIFFER
   0x0019  6d(m)x1                             73(s)x7 20( )x2 5c(\)x1             DIFFER
   0x001a  6c(l)x1                             69(i)x7 76(v)x1 20( )x1 5c(\)x1     DIFFER
   0x001b  20( )x1                             6f(o)x7 65(e)x1 20( )x1 64(d)x1     PARTIAL
   0x001c  76(v)x1                             6e(n)x7 72(r)x1 28(()x1 74(t)x1     DIFFER
   0x001d  65(e)x1                             3d(=)x7 73(s)x2 64(d)x1             DIFFER
   0x001e  72(r)x1                             22(")x7 69(i)x2 ff(.)x1             DIFFER
   0x001f  73(s)x1                             31(1)x7 6f(o)x1 6d(m)x1 21(!)x1     DIFFER
   0x0020  69(i)x1                             2e(.)x7 6e(n)x1 70(p)x1 31(1)x1     DIFFER
   0x0021  6f(o)x1                             30(0)x7 3d(=)x1 6c(l)x1 32(2)x1     DIFFER
   0x0022  6e(n)x1                             22(")x8 65(e)x1 37(7)x1             DIFFER
   0x0023  3d(=)x1                             3f(?)x7 31(1)x1 29())x1 28(()x1     DIFFER
   0x0024  22(")x1                             3e(>)x7 2e(.)x1 20( )x1 2b(+)x1     DIFFER
   0x0025  31(1)x1                             0a(.)x7 30(0)x1 20( )x1 2a(*)x1     DIFFER
   0x0026  2e(.)x1                             3c(<)x7 22(")x1 23(#)x1 21(!)x1     DIFFER
   0x0027  30(0)x1                             21(!)x8 3f(?)x1 46(F)x1             DIFFER
   ... (24 more divergent offsets)
==== MECHANISM CONTEXT (involved fuzzers only) ====
--- cmplog ---
**Instrumentation**: naive's edge counters **plus** integer-CMP
interception (`__sanitizer_cov_trace_cmp1/2/4/8`) and
string/memory-CMP interception (`__sanitizer_weak_hook_strcmp`,
`__sanitizer_weak_hook_memcmp`, etc.). Each CMP callback records
both operands into a per-execution `CmpLogObserver` buffer keyed by
PC.

**Feedback**: same edge-bucket signal as naive. The CMP buffer is
consumed by the mutator, not by feedback.

**Mutators**: naive's havoc + token stack **plus** `I2SRandReplace`.
`I2SRandReplace` reads the post-execution `CmpLogObserver` buffer,
picks a CMP entry, scans the input for byte sequences matching one
operand, and substitutes the other operand at those offsets.

**Observed `mutation_op` in seed metadata**:

havoc/token names; **plus** silent ParentInfo-only entries
(`mutation_op = -` in lineage output) that — in cmplog/vpc only —
indicate an I2SRandReplace find under the current build. The dash
rows are exclusive to cmplog and value_profile_cmplog **within the
original 4-fuzzer canonical set**; there their presence in a winning
seed's ancestor chain is direct (lower-bound) evidence of I2S
contribution.

**Per-execution cost**: edge increment + one callback per intercepted
CMP per execution + post-execution CMP-buffer processing.

--- value_profile_cmplog ---
**Instrumentation**: union of cmplog and value_profile — edge counters,
per-execution CMP buffer (`CmpLogObserver`), and CMP_MAP gradient buckets.

**Feedback**: edge-bucket + CMP_MAP-bucket signals.

**Mutators**: naive's havoc + token stack **plus** `I2SRandReplace`.

**Observed `mutation_op` in seed metadata**: havoc/token names; **plus**
silent ParentInfo-only entries (`mutation_op = -` in lineage) — same
floor signal as cmplog. See the cmplog section's
`TODO(i2s-logging-bug)` note.

**Per-execution cost**: edge increment + CMP-buffer record + CMP_MAP
update per intercepted CMP per execution.

==== TASK ====
WRITE EXACTLY ONE FILE:
  prompts/libxml2_7352.analysis.json

SCHEMA (every field is mandatory; missing or empty fields = analysis failure). The example below uses `//` comments for guidance — REMOVE all `//` lines and inline `//` comments from your emitted JSON (standard JSON does not allow comments).

{
  "branch_id": 7352,
  "target": "libxml2",
  "summary_one_line": "string, <=25 words, the input feature required to take the winning side",
  "pair_decision": "single_feature",
    // pick EXACTLY ONE of: "single_feature" | "multi_feature"
    // decisive pairs at this branch: [value_profile_cmplog>cmplog (value_profile)]
  "hypotheses": [
    {
      "covers_pairs": ["cmplog>naive (I2S)"],
        // labels MUST match exactly as in DECISIVE PAIRS (e.g. "cmplog>naive (I2S)")
      "what_input_feature": "concrete description of the bytes/structure required",
      "why_winner_satisfies": "what about the winner inputs meets the requirement",
      "why_loser_doesnt": "what is missing in the loser inputs",
      "mechanism_attribution": "free text — explain which fuzzer technique enables the winner; must agree with claimed_mechanism below"
    }
    // pair_decision="single_feature" => exactly 1 hypothesis whose covers_pairs lists ALL decisive pairs
    // pair_decision="multi_feature"  => 2+ hypotheses, each covers_pairs listing its subset
  ],
  "evidence_trail": [
    {
      "claim": "atomic factual claim (1 sentence)",
      "cited_section": "BLOCKER",
        // pick the canonical short name of the cited section, one of:
        //   BLOCKER | TRIAL VECTOR | DECISIVE PAIRS | SOURCE CONTEXT |
        //   HIT-COUNT DIVERGENCE | DIVERGENT BRANCHES | BRANCH SEEDS |
        //   BYTE DIFF | MECHANISM CONTEXT
        // validator accepts the full section header too (e.g. "BYTE DIFF (W vs L at common offsets)")
      "cited_locator": "offsets 0x06-0x0f | L1701 | seed_id ab12... | etc.",
      "exact_quote": "verbatim substring of the prompt — COPY-PASTE, do not paraphrase"
    }
    // at least ONE entry per hypothesis sub-field (what / why_winner / why_loser / mechanism)
  ],
  "mechanism_consistency_check": {
    "claimed_mechanism": "I2SRandReplace",
      // pick EXACTLY ONE — the technique that enables the WINNER:
      //   comparison-solving (roadblock dimension):
      //     "I2SRandReplace"     (cmplog / vpc input-to-state substitution)
      //     "CMP_MAP gradient"   (vp / vpc Hamming/prefix-distance feedback)
      //   coverage-granularity (feedback dimension):
      //     "context-sensitive coverage" (naive_ctx CtxHook: (call-context, edge) pairs are new coverage)
      //     "ngram coverage"            (naive_ngram4: N-gram edge tuples are new coverage)
      //   mutation/scheduling (mutation dimension):
      //     "grimoire structural"  (grimoire GeneralizationStage + structural/grammar recombination)
      //     "mopt mutation"        (mopt: MOpt-scheduled mutation-operator probabilities)
      //     "calibrated energy"    (minimizer/fast: corpus-minimization / calibrated power schedule)
      //     "aflfast rarity"       (fast: AFLFast rare-edge power schedule)
      //   baseline / fallback:
      //     "havoc-only"         (lucky havoc byte mutation, no CMP introspection)
      //     "token-replace"      (TokenInsert/TokenReplace dictionary mutations)
      //     "other"              (genuinely cannot classify — NOT a substitute for a known technique above)
    "verified_in_lineage": true,
      // pick true or false
    "verification_method": "ran `python3 tools/db_query.py lineage --branch 7352 --role W --fuzzer cmplog --trial 1 --seed <ID>` and observed an I2S-floor row (mutation_op = -) at depth 19 of the chain"
  },
  "falsifiability": {
    "would_be_refuted_by": "ONE concrete observation that, if true, would kill this hypothesis (something a synthetic experiment could observe, not a story)"
  },
  "weakest_evidence_point": "one sentence naming your single most uncertain claim",
  "confidence": "medium"
    // pick EXACTLY ONE of: "high" | "medium" | "low"
}
