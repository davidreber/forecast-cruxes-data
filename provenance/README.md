# Recorded reranker scores

The authors' recorded reranker scores from the paper's runs. They are
provenance, not inputs: nothing in the analysis reads them. Their one use is
the raw-score drift check of the verification,

    cruxes-verify compare --artifact RELEASE --work-dir WORK --recorded-scores RELEASE/provenance/scores

which, wherever the rerun and the recorded run scored the same ordered pair of
premises under the same criterion (pairs are matched by premise text, because
the two runs select different premises), requires the scores to agree within
0.05 (three times the measured drift between two runs of the same pairs,
0.01611, rounded).

Built 2026-09-24T18:19:39+00:00 by `artifact_builder/build_release.py` in the
verification repository, alongside this data release, and shipped unchanged.

`scores/<dataset>/<criterion>/<question_id>.json` (59 March Madness games and
15 Metaculus questions, three criteria each: 222 files) holds the reranker
scores the research code recorded in the runs on the deduplicated pools that
produced the paper's numbers (2026-09-23), at 25 premises a side, keyed by
premise text: `introspected` and `extracted` list the 25 premises of each
side, and `pos_fwd`, `pos_rev`, `neg_fwd`, `neg_rev` are the 50 by 50 score
matrices over them. The texts were reconstructed with the research code's
selection applied to the deduplicated pools, since the recorded caches store
item ids only; the number of premises is checked against each cache. Each
file's `provenance` names its cache (a path on the authors' cluster, not
shipped) and the cache's SHA-256.
