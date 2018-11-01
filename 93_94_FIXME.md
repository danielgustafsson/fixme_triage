
## contrib/pg_upgrade/server.c:13
	```
	/*
	 * GPDB_93_MERGE_FIXME: include introduced in 3db38b0ceffd95be81573c884f4b,
	 * remove the local define and include at that point instead
	 */
	```
* Classification: security
* Severity: 1
* Complexity: 5

This awaits the above referenced commit which came in a point-release, and is
thus not yet merged (it will be merged via a point-release from 9.4 in commit
928bca1a30d7e05cc3857a99e27aa8ed08ed2fac shipped in 9.4.17). The reason for a
severity of 1 is that the referenced commit is a CVE fix.

This FIXME was removed and turned into a 9.4 FIXME since it actually won't be
solved by code introduced in 9.3:
	```
	/*
	 * GPDB_94_MERGE_FIXME: include introduced in 928bca1a30d7e05cc3857a99e27a
	 * which shipped as 9.4.17.  Remove the local define and use the definition
	 * in the included header once we've merged with the 9.4 minor releases.
	 */
	```

## contrib/pg_xlogdump/compat.c:113
	```
	// GPDB_93_MERGE_FIXME: I hardcoded dbid 0 here, just to make this compile.
	// Where do we get the actual value?
	if (!path[0])
		snprintf(path, MAXPGPATH, "%s_db%d", GP_TABLESPACE_VERSION_DIRECTORY,
	```
* Classification: debugging
* Severity: 3
* Complexity: 3

## gpcontrib/gp_sparse_vector/sparse_vector.h:42
	```
	/*
	 * GPDB_93_MERGE_FIXME: Shouldn't this just be sizeof(int32) + sizeof(int32). OR
	 * use similar to other header routines use offset of data.
	 */
	```
* Classification: debugging
* Severity: 3
* Complexity: 3

## src/backend/access/transam/twophase.c:1327
	```
	// GPDB_93_MERGE_FIXME: Is ThisTimeLineID correct here? Do we need to
	// fetch prepare records for past timelines, after promotion?
	```
* Classification: replication 
* Severity: 2 
* Complexity: 2

## src/backend/access/transam/twophase.c:1433
	```
	/*
	 * GPDB_93_MERGE_FIXME: GPDB used to do XLogCloseReadRecord() and then read,
	 * do we need to perform something similar with new interface.
	 *
	 * GPDB_93_MERGE_FIXME: It's a bit inefficient to allocate a new XLogReader
	 * object for every call. We could avoid opening+closing every WAL segment,
	 * if we kept the XLogReader object around. Or reused xlog.c's openLogFile
	 * file descriptor here.
	```
* Classification: replication
* Severity: 2
* Complexity: 3

This may not be that severe when investigated, but anything replication has
the potential to be severe so opting for safe over sorry here.

## src/backend/access/transam/xlog.c:291:
	```
	static bool recoveryTargetIsLatest = false; // GPDB_93_MERGE_FIXME: should this be set somewhere?
	```
* Classification: replication
* Severity: 2
* Complexity: 3

There is a comment in the code that GPDB doesn't support archive recovery, and
this ties into that. At the very least this needs to be properly documented in
a comment.
	
## src/backend/catalog/heap.c:1797
	```
	case RELKIND_RELATION:
		// GPDB_93_MERGE_FIXME: What about MATVIEW?
		break;
	```
* Classification: metadata tracking
* Severity: 5
* Complexity: 4

This "only" deals with whether or not to log the createion of material views
in the meta data tracking. Sounds like something we should implement.

## src/backend/cdb/cdbgroup.c:4551
	```
	makeAlias(alias, NULL),
	false, /* lateral? */ // GPDB_93_MERGE_FIXME: can this handle laterals? does it need to?
	TRUE);
	```
* Classification: query planning
* Severity: 2
* Complexity: 2

Needs investigation to ensure we fully support LATERAL.

## src/backend/commands/alter.c:764
	```
	case OBJECT_EXTPROTOCOL:
		// GPDB_93_MERGE_FIXME: this probably could be refactored to
		// follow the generic case below
	```
* Classification: refactoring
* Severity: 5
* Complexity: 5

## src/backend/commands/event_trigger.c:1014
	```
 	case OCLASS_COMPRESSION:
 		return false;/*GPDB_93_MERGE_FIXME is these gp types support trigger? */
	```
* Classification: triggers
* Severity: 5 
* Complexity: 5

Proposed fix submitted as PR6126.

## src/backend/commands/extension.c:1273
	```
	CurrentExtensionObject = InvalidOid;
	return InvalidOid;		/* GPDB_93_MERGE_FIXME: can we get the real OID from somewhere? Do we need it? */
	```
* Classification: catalog
* Severity: 5
* Complexity: 5

Proposed fix submitted as PR6127

## src/backend/commands/matview.c:248
	```
	OIDNewHeap = make_new_heap(matviewOid, tableSpace, concurrent,
							   ExclusiveLock, true /* GPDB_93_MERGE_FIXME Is true? */);
	```
* Classification: catalog
* Severity: 5
* Complexity: 5

Proposed fix submitted as PR6128

## src/backend/commands/vacuum.c:724
	```
	/*
	 * GPDB_93_MERGE_FIXME: this is the same test we have in vacuum_rel. Can we
	 * get rid of the redundancy?
	 */
	```
* Classification: refactoring
* Severity: 5 
* Complexity: 4

## src/backend/fts/ftsmessagehandler.c:283
	```
	// GPDB_93_MERGE_FIXME: should we check for FALLBACK_PROMOTE_SIGNAL_FILE, too?
	if (CheckPromoteSignal())
	```
* Classification: availability, replication
* Severity: 3
* Complexity: 3

## src/backend/gpopt/translate/CTranslatorScalarToDXL.cpp:1277
	```
	// GPDB_93_MERGE_FIXME: Fix ORCA to pass the 'funcvariadic' flag through
	// the planning.
	```
* Classification: ORCA

## src/backend/optimizer/plan/plangroupext.c:1906
	```
	/* GPDB_93_MERGE_FIXME: Check whether we could just use
	 * context->current_pathkeys for the function's caller
	 * since the list includes duplicates.
	 */
	```
* Classification: query planning
* Severity: 5
* Complexity: 3

## src/backend/optimizer/plan/setrefs.c:227
	```
	// GPDB_93_MERGE_FIXME: this stopped working, when 'paramlist' was removed from
	// PlannerGlobal. I think list_length(glob->paramlist) should now be glob->nParamExec,
	// but how do you get the individal params? In boundParams perhaps?
	```
* Classification: query planning
* Severity: 2
* Complexity: 2

## src/backend/optimizer/plan/setrefs.c:725
	```
	/*
	 * GPDB_93_MERGE_FIXME: This is a requirement of enhancement,
	 * instead of fixing. The code and assert below is confusing
	 * people who do not know the context. In all, maybe for ao/co
	 * bitmapHeap code should go here?
	```
* Classification: refactoring
* Severity: 5
* Complexity: 4

## src/backend/replication/walsender.c:116
	```
	// GPDB_93_MERGE_FIXME: This was XLogSegsPerFile. But I don't understand why.
	// What was so special about crossing the xlogid boundary? I kept the old
	// value, and maybe it's a suitable one, but it should probably be just a
	// constant like '64' or something.
	```
* Classification: replication
* Severity: 2
* Complexity: 4

## src/backend/replication/walsender.c:2223
	```
	// GPDB_93_MERGE_FIXME: This used to happen, when the "has already been removed"
	// error was thrown. But that's not checked in CheckXLogRemoved(). Do we
	// still need the 'error' field?
	//WalSndCtl->error = WALSNDERROR_WALREAD;
	```
* Classification: replication
* Severity: 4
* Complexity: 4

## src/backend/storage/buffer/localbuf.c:211
	```
	// GPDB_93_MERGE_FIXME: is this TODO comment still relevant?
	// UNDONE: Unfortunately, I think we write temp relations to the mirror...
	```
* Classification: replication
* Severity: 5
* Complexity: 5

## src/backend/tcop/test/Makefile:7
	```
	# GPDB_93_MERGE_FIXME: idle_resource_cleaner target is removed from unit tests
	# because the support for idle session and gang hooks was removed during merge
	# conflict resolution. If those hooks are needed then bring it back and then
	# enable the tests or delete the tests.
	```
* Classification: resource management, mpp
* Severity: 3
* Complexity: 3

## src/backend/utils/adt/numeric.c:691
	```
	* GPDB_93_MERGE_FIXME: The output result for ap_upgrade test had to be updated
	* for 9.3 merge compared to before the merge. Since 9.3 should not have
	* introduced any user-visible change to numeric format, the reason for the
	* change in values should be investigated and impact of the same should be
	* evaluated.
	```
* Classification: upgrade
* Severity: 1
* Complexity: 2

## src/include/catalog/pg_class.h:175
	```
	// GPDB_93_MERGE_FIXME: RELKIND_AOVISIMAP used to be 'm', but that means MATVIEW now.
	// Find all the places where it might be referred to using just 'm', without using this
	// macro. E.g. pg_dump and psql. They might need to also work differently depending
	// on version.
	```
* Classification: catalog
* Severity: 1
* Complexity: 4

## src/include/utils/palloc.h:58
	```
	// GPDB_93_MERGE_FIXME: This mechanism got broken.
	#ifdef USE_ASSERT_CHECKING
	```
* Classification: debugging
* Severity: 4
* Complexity: 2

## src/test/isolation/specs/timeouts.spec:47
	```
	# GPDB_93_MERGE_FIXME: The expected output of this test looks slightly
	# different from upstream. There are no "<waiting>" lines. I believe that's
	# because the pg_locks query that isolationtester uses to detect waiting
	# doesn't work correctly in GPDB. It uses pg_backend_id() to get the QE
	# process PID, but if the lock is held by a QE process, it won't match.
	```
* Classification: testing
* Severity: 4
* Complexity: 3

## src/test/regress/expected/create_index.out:2613
	```
	-- GPDB_93_MERGE_FIXME: These are inside a big start/end-ignore block currently.
	-- Even if we don't support CREATE INDEX CONCURRENTLY, we can still support, and
	-- should test, DROP INDEX CONCURRENTLY.
	```
* Classification: mpp, storage
* Severity: 2
* Complexity: 2 

## src/test/regress/expected/qp_functions_in_subquery_optimizer.out:337
	```
	-- GPDB_93_MERGE_FIXME: The plans, and hence the error message, for these
	-- queries changed in the 9.3 merge, thanks to upstream commit 31c7c642b6.
	-- But the ORCA plans did not change, because that commit didn't affect ORCA.
	```
* Classification: ORCA

## src/test/regress/parallel_schedule:113
	```
	# GPDB_93_MERGE_FIXME: Materialized views have not been "MPP-ified", and don't
	# currently work. Disable test, until it's fixed. (If they still don't work when
	# we make a release, we should add a check in code, to throw a proper "not
	# supported" error message.)
	```
* Classification: mpp
* Severity: ?
* Complexity: 2 

Materialized views are not properly MPPified, do we care about them in 6?

## src/test/regress/sql/event_trigger.sql:158
	```	
	-- GPDB_93_MERGE_FIXME: This query has been modified from upstream's,
	-- to not do a join, because with the original query, the planner would
	-- execute the pg_event_trigger_dropped_objects() function in an entry DB
	-- worker process, not the QD process. That doesn't work, the function
	```
* Classification: test
* Severity: 5
* Complexity: ? 

Is there even something to fix here, other than removing the FIXME comment?

## src/test/regress/sql/join.sql:1087
	```
	-- GPDB_93_MERGE_FIXME: These queries are failing at the moment. Need to investigate.
	-- There were a lot of LATERAL fixes in upstream minor versions, so I'm hoping that
	-- these will get fixed once we catch up to those. Or if not, at least it will be
	-- nicer to work on the code, knowing that there aren't going to be a dozen commits
	```
* Classification: query planning
* Severity: -
* Complexity: - 

Hold until 9.4.x has been merged.

## src/test/regress/sql/regex.sql:30
	```
	-- GPDB_93_MERGE_FIXME: the statistics and/or cost estimation code
	-- in GPDB, combined with the much higher default random_page_cost
	-- setting than PostgreSQL's, means that the planner chooses a seq scan
	-- rather than an index scan for these. Force index scans, so that
	```
* Classification: query planning
* Severity: 4
* Complexity: 3 

## src/test/regress/sql/rowtypes.sql:110
	```
	-- GPDB_93_MERGE_FIXME: In upstream, you naturally get an index only scan for
	-- this query. In GPDB, you get a seq scan, unless you force it. Why is the
	-- cost estimate so different on GPDB?
	```
* Classification: query planning
* Severity: 4
* Complexity: 3 

## GNUmakefile.in:141
	```
	# GPDB_94_MERGE_FIXME: We should stop listing each contrib module here separately.
	```
* Classification: buildsystem, refactoring
* Severity: 5
* Complexity: 5

The rules for what to build in contrib should indeed reside in the contrib
Makefile and not the toplevel Makefile. A single `make -C contrib/ install`
should build everything we consider MPPified.

## contrib/pg_upgrade/gpdb4_heap_convert.c:16
	```
	/*
	 * GPDB_94_MERGE_FIXME: VARSIZE_ANY_EXHDR() contains a TrapMacro, with
	 * assertions enabled. But TrapMacro is not defined in FRONTEND
	 */
	```
* Classification: upgrade
* Severity: 5
* Complexity: 5

This comment should probably just be removed, there seems to be little value
in carrying this assertion into userspace code like the upgrade.

## contrib/pg_upgrade/test_gpdb.sh:111
	```
	# GPDB_94_MERGE_FIXME: This test doesn't work in 9.4 anymore, because
	# freezing no longer resets 'xmin', it just sets a new flag in the
	# tuple header to indicate that the row is frozen. See upstream commit
	# 37484ad2aa. Need to find a new way to verify this.
	```
* Classification: upgrade
* Severity: 3
* Complexity: 3

## gpcontrib/gp_replica_check/Makefile:17
	```
	# GPDB_94_MERGE_FIXME: Enable btree, gin and gist when physical and logical
	# order is equivalent.
	```
* Classification: storage
* Severity: 4
* Complexity: 2

## src/backend/access/appendonly/appendonlywriter.c:1609
	```
	/* GPDB_94_MERGE_FIXME: We used to call this with SnapshotNow,
	 * even though in the row-oriented case above, we use
	 * appendOnlyMetaDataSnapshot. Was that on purpose?
	 * I changed this to also use appendOnlyMetaDataSnapshot.
	```
* Classification: query execution, ao
* Severity: 4
* Complexity: 4

## src/backend/access/bitmap/bitmapxlog.c:740
	```
	/*
	 * GPDB_94_MERGE_FIXME: PostgreSQL 9.4 got rid of the "safe_restartpoint" and
	 * incomplete actions mechanism. See commits 631118fe1e and 40dae7ec53. We
	 * need to do the same for bitmap indexes.
	 */
	```
* Classification: index, query execution
* Severity: 2
* Complexity: 3

## src/backend/access/heap/pruneheap.c:105
	```
	* GPDB_94_MERGE_FIXME: Is that still true, or could we turn this back
	* into an assertion?
	```
* Classification: quey execution
* Severity: 3
* Complexity: 4

## src/backend/access/transam/varsup.c:372
	```
	* GPDB_94_MERGE_FIXME: well, we've made it configurable anyway. Not sure why.
	```
* Classification: oh dear..
* Severity: 2
* Complexity: 3

## src/backend/catalog/index.c:1985
	```
	/* GPDB_94_MERGE_FIXME: Should we do something else here for AO tables? */
	```
* Classification: ao
* Severity: 4
* Complexity: 4

## src/backend/cdb/cdbgroup.c:4766
	```
	NULL, /* GPDB_94_MERGE_FIXME: required_outer. Is NULL ok? */
	```
* Classification: query planning
* Severity: 2
* Complexity: 2

## src/backend/commands/cluster.c:808
	```
	/* GPDB_94_MERGE_FIXME: should we have NewHeap* versions of these functions, too? */
	```
* Classification: ao
* Severity: 3 
* Complexity: 4

## src/backend/commands/tablecmds.c:6496
	```
	// GPDB_94_MERGE_FIXME: tableoid removed. should we put it back? It's used
	// in a lot more places in 9.4
	```
* Classification: storage
* Severity: 2
* Complexity: 2

## src/backend/commands/vacuum.c:1786
	```
	/* GPDB_94_MERGE_FIXME: We have had this check here, instead of the above
	 * check that upstrem has. I would be more comfortable if we would list
	 * the relkinds here explicitly, like in upstream..
	 */
	```
* Classification:
* Severity:
* Complexity:

## src/backend/commands/vacuumlazy.c:532
	```
	// GPDB_94_MERGE_FIXME: is 0 dead tuples appropriate for AO tables?
	```
* Classification: refactoring
* Severity: 4
* Complexity: 5

## src/backend/gpopt/translate/CTranslatorQueryToDXL.cpp:162
	```
	// GPDB_94_MERGE_FIXME: WITH CHECK OPTION views are not supported yet.
	// I'm not sure what would be needed to support them; maybe need to
	// just pass through the withCheckOptions to the ModifyTable / DML node?
	```
* Classification: ORCA

## src/backend/gpopt/translate/CTranslatorQueryToDXL.cpp:3373
	```
	/*
	 * GPDB_94_MERGE_FIXME: RangeTblEntry for functions can now contain multiple function calls.
	 * ORCA isn't prepared for that yet. See upstream commit 784e762e88.
	 */
	```
* Classification: ORCA

## src/backend/gpopt/translate/CTranslatorUtils.cpp:274
	```
	/*
	 * GPDB_94_MERGE_FIXME: RangeTblEntry for functions can now contain multiple function calls.
	 * ORCA isn't prepared for that yet. See upstream commit 784e762e88.
	 */
	```
* Classification: ORCA

## src/backend/gpopt/translate/CTranslatorUtils.cpp:282
	```
	/*
	 * GPDB_94_MERGE_FIXME: Does WITH ORDINALITY work? It was new in 9.4. Add a check here,
	 * if it doesn't, or remove this comment if it does.
	 */
	```
* Classification: ORCA

## src/backend/optimizer/path/pathkeys.c:1266
	```
	NULL, /* nullable_relids */ /* GPDB_94_MERGE_FIXME: is NULL ok here? */
	```
* Classification: query planning
* Severity: 3
* Complexity: 3

## src/backend/optimizer/path/pathkeys.c:1364
	```
	NULL, /* nullable_relids */ /* GPDB_94_MERGE_FIXME: is NULL ok here? */
	```
* Classification: query planning
* Severity: 3
* Complexity: 3

## src/backend/optimizer/plan/initsplan.c:985
	```
	* GPDB_94_MERGE_FIXME: In GPDB, SEMI JOIN may come here, since
	* GPDB pulls up correlated ANY_SUBLINK. Consider the query
	* below:
	```
* Classification: query planning
* Severity: 2 
* Complexity: 2

## src/backend/optimizer/plan/subselect.c:2544
	```
	* GPDB_94_MERGE_FIXME: should we do something about params in
	* the function expressions, like for FunctionScan nodes below?
	```
* Classification: query planning
* Severity: 3
* Complexity: 3

## src/backend/optimizer/plan/transform.c:292
	```
	// GPDB_94_MERGE_FIXME: replace_sirvf_rte() is broken, it needs to be refactored
	// for the change that a FunctionScan node can now contain multiple RangeTblFunctions.
	// However, this will be obsoleted/changed also by PR:
	// https://github.com/greenplum-db/gpdb/pull/5477.
	```
* Classification: query planning
* Severity: 4 
* Complexity: 4

The referenced PR has been pushed, time to address.

## src/backend/optimizer/plan/transform.c:544
	```
	/* GPDB_94_MERGE_FIXME: The SIRV transformation can't handle WITH ORDINALITY
	 * currently */
	```
* Classification: query planning 
* Severity: 3
* Complexity: 3

## src/backend/parser/parse_relation.c:600
	```
	* GPDB_94_MERGE_FIXME: Could we make that work somehow? Resurrect
	* t_tableOid, maybe? I think we'd need it for logical decoding as well.
	```
* Classification:
* Severity:
* Complexity:

## src/backend/parser/parse_relation.c:1398
	```
	/* GPDB_94_MERGE_FIXME: What happens if you have 'coldeflist', and a DESCRIBE callback? */
	```
* Classification: replication
* Severity: 2
* Complexity: 2

## src/backend/parser/parse_utilcmd.c:3669
	```
	/* GPDB_94_MERGE_FIXME: this function used to be responsible, and we had some
	 * more complicated logic here for partitions:
	```
* Classification: query execution
* Severity: 4 
* Complexity: 3

## src/backend/replication/logical/decode.c:141
	```
	/*
	 * GPDB_94_MERGE_FIXME: logical decoding hasn't been implemented for
	 * append-only tables yet.
	 */
	```
* Classification: ao
* Severity: 2
* Complexity: 2

## src/backend/replication/logical/reorderbuffer.c:3034
	```
	/* GPDB_94_MERGE_FIXME: t_tableOid has been removed in GPDB. Does this work
	 * at all without it? */
	```
* Classification: replication
* Severity: 2
* Complexity: 4

## src/backend/tcop/pquery.c:1940
	```
	* GPDB_94_MERGE_FIXME: cannot do catalog lookups, if we're not in a
	* transaction. superuser() requires a catalog lookup (unless the
	* current userid is cached).
	```
* Classification: query execution
* Severity: 3
* Complexity: 4

## src/backend/utils/adt/datetime.c:2729
	```
	/* GPDB_94_MERGE_FIXME: Why does GPDB have this special case for this case? */
	/* yyyymmddhhmmss? */
	```
* Classification: query execution
* Severity: 4
* Complexity: 4

## src/backend/utils/cache/relcache.c:1739, src/backend/utils/cache/relcache.c:1744
	```
	/* GPDB_94_MERGE_FIXME: We get here in abort processing, when we
	 * call getCdbComponentDatabases() to figure out how to reconnect or
	 * something. Temporarily disable this assertion during abort processing.
	 * But we really should stop doing cataloglookups outside a transaction.
	 */
	```
* Classification: refactoring, query execution
* Severity: 4
* Complexity: 3

## src/backend/utils/mmgr/mcxt.c:96:1079
	```
	/*
	 * GPDB_94_MERGE_FIXME: Disabled temporarily, we were unsafe things in GPDB.
	 * Fix all the failures and re-enable this later.
	 */

	// GPDB_94_MERGE_FIXME: same as AssertNotInCriticalSection
	```
* Classification: memory
* Severity: 1
* Complexity: 2

## src/backend/utils/resscheduler/resscheduler.c:916
	```
	/*
	 * GPDB_94_MERGE_FIXME: cannot do catalog lookups, if we're not in a
	 * transaction. Just play dumb, then. Arguably, abort processing
	 * shouldn't be governed by resource queues, anyway.
	 */
	```
* Classification: query execution, resource manager
* Severity: 4
* Complexity: 4

## src/backend/utils/time/snapmgr.c:297
	```
	* GPDB_94_MERGE_FIXME: This is typically the way SnapshotNow was.
	* I think it makes sense to use a local snapshot for this.. But
	* update comments, and verify all the places where this is used.
	```
* Classification: replication
* Severity: 2
* Complexity: 3 

## src/bin/pg_dump/pg_dump.c:14665
	```
	* GPDB_94_MERGE_FIXME: Upstream uses ALTER TABLE ONLY
	* below. Not sure if gpdb needs to use ONLY or not. For
	* now using the gpdb version of query.
	```
* Classification: backup
* Severity: 3
* Complexity: 4 

## src/bin/pg_dump/pg_dump.c:14934
	```
	* GPDB_94_MERGE_FIXME: Why gpdb doesn't pass conditionally
	* SECTION_PRE_DATA or SECTION_POST_DATA based on tbinfo->postponed_def
	* similar to upstream.
	```
* Classification: backup
* Severity: 3
* Complexity: 4

## src/include/nodes/relation.h:69
	```
	/* GPDB_94_MERGE_FIXME: does numPureOrderedAggs include WITHIN GROUP aggs? Should it? */
	```
* Classification: query planning
* Severity: 2
* Complexity: 3


## src/include/postgres.h:143
	```
	/* GPDB_94_MERGE_FIXME: The va_len_1be field was changed to va_tag in PostgreSQL 9.4.
	 * There were comments here that va_len_1be was ignored in GPDB. Does this change to
	 * va_tag work correctly with pg_upgrade in GPDB?
	 */
	```
* Classification: upgrade
* Severity: 2
* Complexity: 4 

## src/include/storage/lwlock.h:220
	```
	// GPDB_94_MERGE_FIXME: do these still work? Are they still needed?
	```
* Classification: debugging tools
* Severity: 5
* Complexity: ? 

Nope, horribly broken..

## src/test/isolation/isolation_schedule:5
	```
	# GPDB_94_MERGE_FIXME: Since the introduction of Global Deadlock Detector, an
	# UPDATE/DELETE no longer acquires ExclusiveLock. So we could perhaps enable
	# some of these now.
	# GPDB_94_MERGE_FIXME: But SELECT FOR UPDATE still acquires a whole-table
	# ExclusiveLock. Why?
	```
* Classification: query execution
* Severity: 3
* Complexity: 3

## src/test/regress/greenplum_schedule:29
	```
	# GPDB_94_MERGE_FIXME: explain_format test was dropped for below group. It's
	# failing without asserts but passing with asserts. Need investigation for the
	# reason, fixed and added back.
	```
* Classification: query execution, testing
* Severity: 3
* Complexity: 3

## src/test/regress/sql/arrays.sql:472
	```
	-- GPDB_94_MERGE_FIXME: ORCA currently creates an incorrect plan for this
	-- query. See https://github.com/greenplum-db/gpdb/issues/5930. Disable
	-- ORCA to silence the failure, until that's fixed.
	```
* Classification: ORCA

## src/test/regress/sql/subselect.sql:447
	```
	-- GPDB_94_MERGE_FIXME: ORCA plan is correct but very pricy. Should we fallback to planner?
	```
* Classification: ORCA

## src/test/regress/sql/union.sql:216
	```
	-- GPDB_94_MERGE_FIXME: GPDB would choose seqscan rather than indexscan for t1.
	-- This is a side-effect of disabling pull-up for simple union all in GPDB.
	```
* Classification: query planning
* Severity: 4
* Complexity: 2

