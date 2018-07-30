# PostgreSQL 9.0 Merge FIXMEs Triage

Each FIXME is ranked for complexity and severity, with 1 being the lowest and
5 the highest. This is done highly subjective.

## contrib/tsearch2/tsearch2.c:419
	/* 
	 * GPDB_90_MERGE_FIXME: previously, if the fcinfo->context is a
	 * WindowAggState, we would assign the partcontext to aggcontext, but
	 * now AggCheckCallContext is assigning the aggcontext field of the
	 * WindowAggState instead. Need to understand why this has changed, and
	 * if it is correct to get the partcontext here.
	 */

* Classification: query execution
* Complexity: 3
* Severity: 3
* Status: FIXME


## contrib/xlogdump/Makefile:24
	
	# GPDB_90_MERGE_FIXME: Force majorversion to 9.0. Revert this once we bump the
	# version number from 8.5devel to 9.0devel.
	majorversion=90
	#majorversion=`echo $(PG_VERSION) | sed -e 's/^\([0-9]*\)\.\([0-9]*\).*/\1\2/g'`

* Classification: server utilities 
* Complexity: 1
* Severity: 3
* Status: FIXME

Need to use the right majorversion for the tool to work properly. Trival to
fix, PR posted to address.


## src/backend/cdb/cdbllize.c:198
	/*
	 * GPDB_90_MERGE_FIXME: Because of this block, we were marking a dummy
	 * UPDATE that the planner could prove to be a no-op, e.g
	 * "update target set b = 10 where c = 10;" where 'target' is partitioned
	 * table and there is no partition for c = 10. The planner would turn
	 * that into just a dummy Result node. This block nevertheless marked the
	 * query with 'resultSegments = true', causing the executor to dispatch
	 * the query. That caused confusion at commit time, because there was no
	 * active two-phase transaction in the segmetns, but the QD thought that
	 * there should be.
	 *
	 * There is a test case like that in 'update_gp'. I have a feeling that
	 * we should still have code like this in apply_motion_mutator(), when
	 * traversing into a ModifyTable node. But everything seems to work
	 * without it. Go figure...
	 */

* Classification: query planning/execution
* Complexity: 4
* Severity: 1
* Status: FIXME

Existing testcases work without this, no data correctness issue but may have
performance implications


## src/backend/cdb/cdbmutate.c:1218 
	/*
	 * GPDB_90_MERGE_FIXME: We also need to change width and cost here. But since the plan has been
	 * generated at this stage, it is not clear how we could recalculate the cost.
	 */

* Classification: query planning/execution
* Complexity: 3
* Severity: 1
* Status: FIXME

As the comment states, the plan has already been generated at this point and
updated costs wont cause a replanning. Why do we need to update the costs?
For EXPLAIN?


## src/backend/cdb/cdbmutate.c:3753
	/*
	 * If there is no distribution key, don't do direct dispatch.
	 *
	 * GPDB_90_MERGE_FIXME: Is that the right thing to do? Couldn't we
	 * direct dispatch to any arbitrarily chosen segment, in that case?
	 */

* Classification: query planning/execution
* Complexity: 3
* Severity: -
* Status: TODO

Doing direct dispatch via `cdbhashnokey()` is a new feature so it really
shouldn't be a merge FIXME at all.


## src/backend/commands/analyze.c:647
	// GPDB_90_MERGE_FIXME: Need to implement 'acuire_inherited_sample_rows_by_query'

* Classification: statistics/analytics
* Complexity: 4 
* Severity: -
* Status: TODO

It's true that we probably should adopt a blocklevel sampling method like in
upstream, but `acquire_sample_rows_by_query()` currently does grab the rows
from partition hierarchies so it's not really a FIXME. No functionality is
lost, Greenplum simply does things another way (currently).


## src/backend/commands/explain.c:488
	/*
	 * GPDB_90_MERGE_FIXME: we need to backport more from the 9.2 timeframe
	 * to deal with each separate INSTRUMENT_ flag correctly.
	 */

* Classification: statistics/analytics
* Complexity: 1
* Severity: 0
* Status: FIXME

With 9.2 currently being merged, this seems like a FIXME that can be removed.
I can't see anything from a quick browse that we're missing right now.


## src/backend/commands/explain.c:1512 src/backend/commands/explain_gp.c:1782 src/backend/commands/explain_gp.c:1881 src/backend/commands/explain_gp.c:2313
	/* GPDB_90_MERGE_FIXME: In GPDB, these are printed differently. But does that work
	 * with the new XML/YAML EXPLAIN output */

	/*
	 * Extra message text. The text is preformatted in the nodeXXX code into
	 * TEXT format output, it's a GPDB_90_MERGE_FIXME to transform this into
	 * something that all formats can consume
	 */

	/*
	 * Summary by slice
	 *
	 * GPDB_90_MERGE_FIXME: Refactor the Slice statistics printing such that
	 * we can use it in non-TEXT format output.
	 */

	/*
	 * GPDB_90_MERGE_FIXME: handle rollup times printing
	 * if (rollup_gs_times > 1)
	 *	appendStringInfo(es->str, " (%d times)", rollup_gs_times);
	 */

* Classification: statistics/analytics
* Complexity: 3
* Severity: 3
* Status: FIXME

These are all related to Greenplum specific information needing to be changed
to use the new ExplainProperty framework in order to support JSON/YAML etc.
This is currently being worked on.


## src/backend/commands/explain.c:2152
	/*
	 * If it's EXPLAIN ANALYZE, show tuplesort stats for a sort node
	 *
	 * GPDB_90_MERGE_FIXME: The sort statistics are stored quite differently from
	 * upstream, it would be nice to rewrite this to avoid looping over all the
	 * sort methods and instead have a _get_stats() function as in upstream.
	 */

* Classification: statistics/analytics
* Complexity: 2
* Severity: -
* Status: TODO

This is a nice to have refactoring which (probably) speeds up EXPLAIN ANALYZE
and cleans up code. Should become a TODO item.


## src/backend/commands/exttablecmds.c:815
	/* GPDB_90_MERGE_FIXME: add 'force_quote_all' here */

* Classification: query execution
* Complexity: 1
* Severity: -
* Status: TODO

Since we support `force_quote <column>` we should also support the shorthand
`force_quote *` to quote all columns. PR opened to implement this.


## src/backend/executor/execBitmapTableScan.c:52 src/backend/executor/execScan.c:45
	// GPDB_90_MERGE_FIXME: should we have rechecks for AO / AOCS scans?

* Classification: query execution
* Complexity: 3
* Severity: 2
* Status: FIXME

I'm not entirely sure, but I have the feeling that we should have recheck
methods for lossy bitmaps at least.  Not sure if that case is tested in ICW
but if not, that would be nice to add as well (if trivially possible).


## src/backend/executor/nodeDML.c:165
	/*
	 * Initialize es_result_relation_info, just like ModifyTable.
	 * GPDB_90_MERGE_FIXME: do we need to consolidate the ModifyTable and DML
	 * logic?
	 */

* Classification: query execution
* Complexity: 4
* Severity: -
* Status: TODO

9.0 introduced the ModifyTable node, which overlaps with the Greenplum DML
node for a lot of things. Longer term we should probably retire the DML node
and move to using the ModifyTable node if only to make merging easier. This
will most likely be part of the CTE refactoring work.


## src/backend/executor/nodeModifyTable.c:489
	/*
	 * GPDB_90_MERGE_FIXME: do we really need this? It seems like if ORCA
	 * can get us here, we shouldn't worry about it. Commenting to get
	 * alter_table working with ORCA.
	 *
	Assert(planGen == PLANGEN_PLANNER);
	 */

* Classication: query execution
* Complexity: 1
* Severity: 1
* Status: FIXME

I agree with the comment, I don't see why that Assertion was added or what
purpose it would serve.  Seems most reasonable to just remove the comment and
the commented out Assertion altogether.


## src/backend/executor/nodeModifyTable.c:1830
	/* GPDB_90_MERGE_FIXME: This code was heavily refactored in the merge.
	 * This used to be in ExecInsert, in execMain.c. It was also heavily
	 * modified in GPDB, to handle rowmarks differently, and to disable
	 * some sanity checks for ORCA. I did not copy over those GPDB changes,
	 * because I wasn't sure how, and wasn't sure which of the changes
	 * were still needed.
	 */

* Classification: query execution
* Complexity: 3
* Severity: 2
* Status: FIXME

## src/backend/libpq/auth.c:1147
	/*
	 * GPDB_90_MERGE_FIXME: this logic is copied from hashed_passwd_verify;
	 * double-check it (especially the lack of password checks, which we may
	 * need here) and consolidate it somehow so we don't fall out of sync.
	 */

* Classification: security
* Complexity: 1
* Severity: 1
* Status: FIXME

Left as a note-to-self to doublecheck post-merge. Did just that and the FIXME
is ripe for removal. PR submitted to remove this.


## src/backend/nodes/equalfuncs.c:1451 src/backend/nodes/outfuncs.c:2655 src/backend/nodes/equalfuncs.c:2458
	/* GPDB_90_MERGE_FIXME: should we compare altconname? */
	/* GPDB_90_MERGE_FIXME: should we write is_split_part? */
	/* GPDB_90_MERGE_FIXME: should we be comparing encoding? */
	
* Classification: query execution
* Complexity: 2
* Severity: 1
* Status: FIXMEs


## src/backend/utils/mmgr/portalmem.c:790 src/backend/utils/mmgr/portalmem.c:942
	/*
	 * GPDB_90_MERGE_FIXME: This was added in commit 7981c342, to prevent
	 * ExecutorEnd from running in failed transactions. That's fine and dandy,
	 * but unfortunately GPDB relies on ExecutorEnd to for some cleanup
	 * work, like terminating the Gang. So we in GPDB, we must run ExecutorEnd.
	 * We really should refactor the resource management in dispatcher and
	 * gangs, e.g. to use ResourceOwners instead. But until that's done,
	 * we cannot skip ExecutorEnd.
	 */

	// GPDB_90_MERGE_FIXME: Not in READY portals. See comment in AtAbort_Portals.

* Classification: query execution/mpp
* Complexity: 4
* Severity: 2
* Status: FIXME

It's debatable whether this is a FIXME or a TODO, but it's something we have
to get done so we might as well get it done sooner rather than later befre it
starts giving is issues with merging.


## src/backend/optimizer/plan/createplan.c:6676
	/*
	 * Set the distribution of the ModifyTable node itself. If there is only
	 * one subplan, or all the subplans have a compatible distribution, then
	 * we could mark the ModifyTable with the same distribution key. However,
	 * currently, because a ModifyTable node can only be at the top of the
	 * plan, it won't make any difference to the overall plan.
	 *
	 * GPDB_90_MERGE_FIXME: I've hacked a basic implementation of the above for
	 * the case where all the subplans are POLICYTYPE_ENTRY, but it seems like
	 * there should be a more general way to do this.
	 */

* Classification: query planning
* Complexity: ?
* Severity: -
* Status: TODO


## src/backend/optimizer/util/pathnode.c:1878
	// GPDB_90_MERGE_FIXME: this looks very wrong.

* Classification: query planning
* Complexity: 2
* Severity: 2
* Status: FIXME

This is clearly wrong, but it wasn't introduced by the merge but it came in
d4ce0921d80 which refactored semi-join planning. I think this is a copy-paste
that needs to be removed, but it clearly requires more thinking before doing.


## src/backend/postmaster/postmaster.c:5047
	/*
	 * GPDB_90_MERGE_FIXME: This function should be removed once hot
	 * standby can and will be enabled for mirrors.
	 */
	void SignalPromote(void)

* Classification: replication
* Complexity: ?
* Severity: -
* Status: TODO

Seems like a clear-cut TODO item to me, which depends on the replication work.


## src/backend/optimizer/plan/setrefs.c:688
	/* GPDB_90_MERGE_FIXME: How about rowmarks here? Do we need to stash them
	 * in TableFunctionScan? */

* Classification: query planning
* Complexity: 4
* Severity: 3
* Status: FIXME


## src/include/catalog/pg_attribute_encoding.h:6
	/*
	 * GPDB_90_MERGE_FIXME: pg_attribute now has an attoptions field. We should
	 * get rid of this table, and start using pg_attribute.attoptions instead.
	 *

* Classification:
* Complexity: 2
* Severity: -
* Status: TODO

We should definately do this in the 6.x cycle to get rid of this catalog.


## src/test/regress/sql/notin.sql:340
	-- GPDB_90_MERGE_FIXME: We should generate c2 IS NOT NULL derived filter.

* Classification: query planning
* Complexity: 3
* Severity: -
* Status: TODO

The query plan generated is equal to that in PostgreSQL 11, this should be a
TODO for future work instead.


## src/test/regress/sql/notin.sql:353
	-- GPDB_90_MERGE_FIXME: We should be able to push down join filter on param $0 to a result node on top of LASJ (Not in)
	
* Classification: query planning
* Complexity: 3
* Severity: -
* Status: TODO

This is a proposed performance optimization, we already generate correct
results.


## src/test/regress/sql/portals.sql:428
	-- GPDB_90_MERGE_FIXME: This doesn't work correctly. Two issues:
	-- 1. In GPDB, an UPDATE, or FOR UPDATE, locks the whole table. Because of
	--    that, there cannot be concurrent updates, and we don't bother with
	--    LockRows nodes in FOR UPDATE plans. However, in the upstream, the
	--    LockRows node also handles fetching the latest tuple version, if it
	--    was updated in the same transaction, by a *later* command.
	--
	-- 2. Even if we had LockRows in the plan, it still wouldn't work, at least
	--    not always. In PostgreSQL, the LockRows node checks the visibility
	--    when a row is FETCHed. Not before that. So if a row is UPDATEd in
	--    the same transaction, before it's FETCHed, the FETCH is supposed to
	--    see the effects of the UPDATE. In GPDB, however, a cursor starts
	--    executing in the segments, as soon as the DECLARE CURSOR is issued,
	--    so there's a race condition.

* Classification: query execution
* Complexity: 4
* Severity: 4
* Status: FIXME


## src/test/regress/sql/returning.sql:151 src/test/regress/sql/rpt_returning.sql:150
	
	-- GPDB_90_MERGE_FIXME: This CREATE RULE fails in GPDB. Why?
	-- GPDB_90_MERGE_FIXME: This CREATE RULE fails in GPDB. Why?
	
* Classification: query execution
* Complexity: 3
* Severity: 2
* Status: FIXME

This could be because updating views isn't supported in Greenplum, or another
issue with RULEs. Either way it should be looked into. If it's the former then
a better error message should be emitted.
