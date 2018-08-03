
## GNUmakefile.in:51 contrib/file_fdw/file_fdw.c:780
	#$(MAKE) -C contrib/file_fdw $@ # GPDB_91_MERGE_FIXME: disable installation until it's officially supported.
	/* GPDB_91_MERGE_FIXME: do we need damping? */

* Classification: foreign tables
* Severity: 
* Complexity:
* Status: 

Nothing to do here, whomever will do foreign tables will have to look into
these and many more.


## contrib/pg_upgrade/dump.c:27 contrib/pg_upgrade/dump.c:56
	/* GPDB_91_MERGE_FIXME "--quote-all-identifiers */ " --binary-upgrade %s -f %s",
	"\"%s/pg_dump\" %s --schema-only " /* GPDB_91_MERGE_FIXME --quote-all-identifiers " */

* Classification: upgrade
* Severity: 
* Complexity: 1
* Status: FIXME

This should just work now that we've merged with 9.1 (but it didn't), PR
submitted to fix.


## gpAux/Makefile:196
	# GPDB_91_MERGE_FIXME: Our MinGW environment doesn't currently include Python.

* Classification: buildsystem
* Severity:
* Complexity:
* Status: FIXME

It's unclear what we should do about this one, if anything. 


## src/backend/access/bitmap/bitmap.c:111 
	elog(ERROR, "GPDB_91_MERGE_FIXME: bmbuildempty not implemented");

* Classification: query execution
* Severity: 2
* Complexity: 
* Status: FIXME

This function is required for supporting bitmap indexes on UNLOGGED tables. If
we end up not supporting bitmap indexes on unlogged tables then we should at
least tidy up the error message to match what GiST has.


## src/backend/access/external/fileam.c:162
	/*
	 * GPDB_91_MERGE_FIXME: scan->raw_buf_done is used for custom external
	 * table only now. Maybe we could refactor externalgettup_custom() to
	 * remove it.
	 */

* Classification: query execution
* Severity: -
* Complexity: 2
* Status: TODO

It would shave some space off the FileScanDesc structure, but since it's only
used for external table scans which are soon to be replaced by FDW it hardly
seems worth the effort.


## src/backend/access/external/fileam.c:923
	/*
	 * If NextCopyFrom failed, the processed row count will have already
	 * been updated, but we need to update it in a successful case.
	 *
	 * GPDB_91_MERGE_FIXME: this is almost certainly not the right place for
	 * this, but row counts are currently scattered all over the place.
	 * Consolidate.
	 */

* Classification: query execution
* Severity: -
* Complexity: 3 
* Status: TODO

Refactoring and cleaning up the single row error handling is definately a TODO
which we can deal with later. It would be very nice to get done though.


## src/backend/access/external/fileam.c:2068
	/* GPDB_91_MERGE_FIXME: what is this used for? */

* Classification: query execution
* Severity: 1
* Complexity: 1
* Status: FIXME

The simple answer is that it's not used at all AFAICT, since the presence of
a formatter in a CSV external table is already errored out on when the table
is created. PR submitted to clean this up.


## src/backend/catalog/catalog.c:858
	/*
	 * GPDB_91_MERGE_FIXME: check again for a collision with a temp
	 * table (if this is a normal relation) or a normal table (if this
	 * is a temp relation).
	 *
	 * The shared buffer manager currently assumes that relfilenodes of
	 * relations stored in shared buffers can't conflict, which is
	 * trivially true in upstream because temp tables don't use shared
	 * buffers at all. We have to make this additional check to make
	 * sure of that.
	 */

* Classification: catalog/storage
* Severity: 2
* Complexity: 2
* Status: FIXME

The fixme describes what the code is doing rather than what it wants the code
to be changed into. The double check could be rolled into the collision code 
in `GpCheckRelFileCollision()` but that might not be a net improvement. This
may well end up just being a FIXME comment that needs to be removed, but that
should be verified.


## src/backend/catalog/pg_constraint.c
	/*
	 * Determine whether a relation can be proven functionally dependent on
	 * a set of grouping columns.  If so, return TRUE and add the pg_constraint
	 * OIDs of the constraints needed for the proof to the *constraintDeps list.
	 *
	 * grouping_columns is a list of grouping expressions, in which columns of
	 * the rel of interest are Vars with the indicated varno/varlevelsup.
	 *
	 * Currently we only check to see if the rel has a primary key that is a
	 * subset of the grouping_columns.  We could also use plain unique constraints
	 * if all their columns are known not null, but there's a problem: we need
	 * to be able to represent the not-null-ness as part of the constraints added
	 * to *constraintDeps.  FIXME whenever not-null constraints get represented
	 * in pg_constraint.
	 *
	 * GPDB_91_MERGE_FIXME: this does not seem to correctly reject invalid GROUPING
	 * SET queries. Possibly because those were backported from 9.5?
	 */

* Classification: grouping sets
* Severity: 3
* Complexity: 5
* Status: FIXME

This should probably be fixed by backporting the GROUPING SETS from upstream.


## src/backend/cdb/cdbcopy.c:344
	/* GPDB_91_MERGE_FIXME: How should we handle errors here? We used
	 * to append them to err_msg, but that doesn't seem right. Surely
	 * we should ereport()? I put in just a quick elog() for now..
	 */

* Classification: query execution
* Severity: 2
* Complexity: 2
* Status: FIXME

The added `elog()` call will break out of the loop and do what seems to be the
right thing. There is code which is now dead that should be cleaned up though.


## src/backend/cdb/cdbcopy.c:623 src/include/cdb/cdbcopy.h:74 src/backend/commands/copy.c:1939
	/*
	 * End the copy command on all segment databases,
	 * and fetch the total number of rows completed by all QEs
	 * 
	 * GPDB_91_MERGE_FIXME: we allow % value to be specified as segment reject
	 * limit, however, the total rejected rows is not allowed to be > INT_MAX.
	 */

	/*
	 * GPDB_91_MERGE_FIXME: let's consistently use uint64 as type for counting rows
	 * of any kind.
	 */

	int			rejected = 0; /* GPDB_91_MERGE_FIXME: should be uint64! */

* Classification: query execution
* Severity: 3
* Complexity: 2
* Status: FIXME

The issue is that we count rejected rows with an integer and successful rows
with an int64, and we define `rejectlimit` with an integer. All of these should
be using an uint64.


## src/backend/cdb/cdbcopy.c:648
	/*
	 * Don't try to end a copy that already ended with the destruction of the
	 * writer gang. We know that this has happened if the CdbCopy's
	 * primary_writer is NULL.
	 *
	 * GPDB_91_MERGE_FIXME: ugh, this is nasty. We shouldn't be calling
	 * cdbCopyEnd twice on the same CdbCopy in the first place!
	 */

* Classification: query execution
* Severity: 1
* Complexity: 2
* Status: FIXME

With the recent rewrite of cdbCopy, I'm not sure this comment applies anymore.
This FIXME is to re-read the code and see if we indeed could reach this code
path multiple times.


## src/backend/commands/copy.c:39
	/*
	 * GPDB_91_MERGE_FIXME: eww. cdbsreh.c does this PQArgBlock redefinition too, to
	 * avoid the conflict with libpg-fe, above. Probably want to backport 01cca2c1
	 * from upstream.
	 */

* Classification: code hygiene
* Severity: 1
* Complexity: 1
* Status: FIXME

Yes. This is trivially fixable, PR submitted to address this.


## src/backend/commands/copy.c:282
	/* GPDB_91_MERGE_FIXME: passing through a global variable like this is ugly */

* Classification: code hygiene
* Severity: 1
* Complexity: 1
* Status: TODO

Yeah, this is pretty ugly but not a showstopper for going onto 9.3 (but if we
have time we should really get to it).


## src/backend/commands/copy.c:308
	/*
	 * ..
	 *
	 * GPDB_91_MERGE_FIXME: that's a nice theory, but the current implementation
	 * is a lot more dumb: The QD parses every row fully, and sends all
	 * precomputed values to each QE. Therefore, with the current implementation,
	 * the QD will easily become a bottleneck, if the input functions are
	 * expensive. Before the refactoring during the 9.1 merge, there was no
	 * special QD->QE protocol. Instead, the QD reconstructed each line in the
	 * same format as the original file had, interjecting any DEFAULT values into
	 * it. That was fast when only a few columns needed to be evaluated in the QD,
	 * but it was not optimal, but it was pretty complicated, and required some
	 * majore surgery to the upstream NextCopyFrom and other functions.
	 * ..
	 */

* Classification: query execution
* Severity: -
* Complexity: 5
* Status: TODO

Making COPY perform better is a nice goal, but not part of the post-merge
cleanup activities. Remove the comment and mark as TODO. 


## src/backend/commands/copy.c:955
	/* GPDB_91_MERGE_FIXME: this should probably be done earlier, e.g. in parser */

* Classification: query execution
* Severity: -
* Complexity: 2
* Status: TODO

Yes, the options should be added in the parser and not here. This is pre-
existing thing that we can make into a TODO.


## src/backend/commands/copy.c:982
	/* GPDB_91_MERGE_FIXME: why not ? */

* Classification: query execution
* Severity: 1
* Complexity: 2
* Status: FIXME/TODO

Thats a good question, it should be investigated but it will most likely become
a TODO.


## src/backend/commands/copy.c:1042
	/*
	 * GPDB_91_MERGE_FIXME: is it possible to get to this point in the code
	 * with a temporary relation that belongs to another session? If so, the
	 * following code doesn't function as expected.
	 */

* Classification: query execution
* Severity: 3
* Complexity: 2
* Status: FIXME

Needs investigation.


## src/backend/commands/copy.c:1127
	/*
	 * GPDB_91_MERGE_FIXME: ExecutorStart() is called in BeginCopyTo,
	 * but the TRY-CATCH block only starts here. If an error is
	 * thrown in-between, we would fail to call mppExecutorCleanup. We
	 * really should be using a ResourceOwner or something else for
	 * cleanup, instead of TRY-CATCH blocks...
	 * ..
	 */

* Classification: query execution
* Severity: -
* Complexity: 3
* Status: TODO

Using resource owners to clean up is a long-standing TODO which we really should
get to, but it's not a hard requirement for starting 9.3.

## src/backend/commands/copy.c:1989

	/*
	 * GPDB_91_MERGE_FIXME: SREH handling seems to be handled in a different
	 * place for every type of copy. This should be consolidated with the
	 * others.
	 */

* Classification: query execution
* Severity: -
* Complexity: 2
* Status: TODO


## src/backend/commands/copy.c:2667
	return 0; /* GPDB_91_MERGE_FIXME: does this make sense? */

* Classification: query execution
* Severity: 2
* Complexity: 2
* Status: FIXME

This needs to be validated, it seems quite sane but it I also wonder if we
can break out earlier in this case?


## src/backend/commands/copy.c:4882
	/* GPDB_91_MERGE_FIXME: The idea here would be to only call the
	 * input function for the fields we need in the QD. But for now,
	 * screw performance. */

* Classification: query execution
* Severity: -
* Complexity: 3
* Status: TODO

This would be a neat performance improvement.


## src/backend/commands/copy.c:5177
	/*
	 * GPDB_91_MERGE_FIXME: do we need to worry about toasted datums here?
	 * In theory, I suppose you could have a default value that gets evaluated
	 * in the master, and the default value comes from another table as a toast
	 * pointer.
	 */

* Classification: query execution
* Severity: 4
* Complexity: 3
* Status: FIXME

We need to figure out this one, and at the very least elog(ERROR ..) out on
this case with a TODO comment should we find that it's possible.


## src/backend/commands/copy.c:5535 src/backend/commands/copy.c:5568 src/backend/commands/copy.c:5590
	/*
	 * GPDB_91_MERGE_FIXME: these commented-out blocks (as well
	 * as the restructured newline checks) are here because we
	 * allow the user to manually set the newline mode, and
	 * therefore don't error out on bare CR/LF in the middle of
	 * a column. Instead, they will be included verbatim.
	 * ..
	 */

	#if 0 /* GPDB_91_MERGE_FIXME: see above. */
	#if 0 /* GPDB_91_MERGE_FIXME: see above. */

* Classification: query execution
* Severity: -
* Complexity: 3
* Status: TODO

This is Greenplum specific functionality which collides with upstream, so in
the end we might just leave this as is but I guess we should figure out if 
there is anything we can submit upstream here?


## src/backend/cdb/cdbgroup.c:2102
	/* GPDB_91_MERGE_FIXME: we used to pass
	 * "ctx->agg_counts->numAggs - ctx->agg_counts->numOrderedAggs + 1"
	 * as the "numAggs" argument. That argument is gone, make_agg
	 * gets that number directly from agg_costs now. So this changed
	 * the costing slightly. Maybe that's OK, or not?
	 */

* Classification: query planning
* Severity: 2
* Complexity: 2
* Status: FIXME

This was changed in e6a30a8c3c8 in order to properly account for startup costs
in aggregates. Reading this I can't see a reason why this would be a problem,
but the interplay with the `cost_Xphase_aggregation()` functions should be
looked at before removing the comment.


## src/backend/cdb/cdbgroup.c:3348
	* discover columns that need to be added back to the grouping target list.
	*
	* GPDB_91_MERGE_FIXME: This duplication is brittle. We should modify our
	* grouping planner to remove the assumptions on target list structure, and find
	* a better way to construct a correct series of Aggref stages.
	*/

* Classification: query planning
* Severity: -
* Complexity: 2
* Status: TODO


## src/backend/cdb/cdbgroup.c:3597
	/*
	 * GPDB_91_MERGE_FIXME: fix up the grps_tlist to also include any columns
	 * that have a functional dependency on a primary key in the GROUP BY clause
	 * (i.e. they are part of a relation that's already being grouped by a
	 * primary key, which guarantees that tuples composed of these columns are
	 * unique). We can avoid rewriting several assumptions by doing this, but
	 * it's really ugly...
	 */

* Classification: query planning
* Severity: 2
* Complexity: 2
* Status: FIXME

This may well turn into a TODO after some investigation, but we need to ensure
it's sane (although ugly) before we make it a TODO in that case.


## src/backend/cdb/cdbhash.c:783
	/*
	 * GPDB_91_MERGE_FIXME:
	 * "pg_node_tree" is introduced in PG 9.1 to be
	 * the type for nodeToString output. It can be
	 * coerced to, but not from, text.
	 * ..
	 */

* Classification: gpmgmt utilities
* Severity: 1
* Complexity: ?
* Status: FIXME

gpcheckcat seems to test whether the datatypes that should be useable as
distribution keys are also GPDB hashable. This check needs to cope with types
such as `pg_node_tree` so we can remove this hack.


## src/backend/cdb/cdbpartition.c:5132
	RELPERSISTENCE_PERMANENT, /* GPDB_91_MERGE_FIXME: what if it's unlogged or temp? Where to get a proper value for this? */

* Classification: query execution
* Severity: 2
* Complexity: 2
* Status: FIXME

From a quick skim, can't we just use `rel->rd_rel->relpersistence` here? Either
way we need to resolve this.


## src/backend/cdb/cdbvars.c:546
	// GPDB_91_MERGE_FIXME: we don't have access to 'source' anymore
	//if (source != PGC_S_DEFAULT)

* Classification:
* Severity: 1
* Complexity: 1
* Status: FIXME


## src/backend/commands/explain.c:603
	/*
	 * GPDB_91_MERGE_FIXME: If the target is a partitioned table, we
	 * should also report information on the triggers in the partitions.
	 * I.e. we should scan the the 'ri_partition_hash' of each
	 * ResultRelInfo as well. This is somewhat academic, though, as long
	 * as we don't support triggers in GPDB in general..
	 */

* Classification: explain / statistics
* Severity: -
* Complexity: 1
* Status: TODO


## src/backend/commands/tablecmds.c:4042
	/* 
	 * Phase 3: scan/rewrite tables as needed. If the data is in an external
	 * table, no need to rewrite it or to add toast.
	 * GPDB_91_MERGE_FIXME: How do we handle FOREIGN TABLEs? Should handle
	 * external tables the same..
	 */

* Classification: query execution
* Severity: 1
* Complexity: 1
* Status: FIXME

Foreign tables are handled in the `ATRewriteTables()` function, to align us
better with upstream we can remove this if-statement and roll it into the
foreign table handling in there.


## src/backend/commands/variable.c:640 
	/*
	 * GPDB_91_MERGE_FIXME: Prior to PostgreSQL 9.1, serializable isolation was
	 * implemented as read committed.  True serializable isolation level is
	 * supported as of PostgreSQL 9.1.  However, Greenplum lacks the support to
	 * detect serialization conflicts using predicate locks at cluster level.
	 * Until that support is implemented, let's keep old behavior by falling
	 * back to repeatable read. Also, for similar reasons guc
	 * transaction_deferrable is not dispatched to QE's as no use
	 * currently. When serializable is fixed make sure to fix dispatching of the
	 * transaction_deferrable guc via DtxContextInfo similar to
	 * transaction_isolation.
	 */

* Classification: transaction isolation
* Severity: -
* Complexity: 5
* Status: TODO

We need to implment serializable transation isolation, which is a TODO.


## src/backend/executor/execMain.c:2161
		/*
		 * GPDB_91_MERGE_FIXME: In Greenplum, views are treated as non
		 * partitioned relations, gp_distribution_policy contains no entry
		 * for views.  Consequently, flow of a ModifyTable node for a view
		 * is determined such that it is not dispatched to segments.
		 * Things get confused if the DML statement has a where clause that
		 * results in a direct dispatch to one segment.  Underlying scan
		 * nodes have direct dispatch set but when it's time to commit, the
		 * direct dispatch information is not passed on to the DTM and it
		 * sends PREPARE to all segments, causing "Distributed transaction
		 * ... not found" error.  Until this is fixed, INSTEAD OF triggers
		 * and DML on views need to be disabled.
		 */

* Classification: query execution
* Severity: 2
* Complexity: 4
* Status: FIXME

We could alternatively live with this limitatation, since we don't really 
support triggers already etc. It's a tradeoff vs merging faster..


## src/backend/executor/nodeDML.c:89 src/backend/executor/nodeDML.c:122 src/backend/executor/nodeDML.c:139 src/backend/executor/nodeDML.c:143
	/* GPDB_91_MERGE_FIXME:
	 * This kind of node is used by ORCA only. If in the future ORCA still uses
	 * DML node, canSetTag should be saved in DML plan node and init-ed by
	 * copying canSetTag value from the parse tree.
	 * ..
	 */

	true, /* GPDB_91_MERGE_FIXME: canSetTag, where to get this? */
	NULL, /* GPDB_91_MERGE_FIXME: oldTuple? */
	!isUpdate, /* GPDB_91_MERGE_FIXME: where to get canSetTag? */

* Classification: ORCA
* Severity: 1
* Complexity: 4
* Status: FIXME/TODO

Dropping the DML node and adapting ORCA to use the new nodes that planner does
might be the answer here, but someone from the ORCA team will have to answer
that.


## src/backend/executor/nodeHashjoin.c:191 
	/* GPDB_91_MERGE_FIXME: do we need to get rid of hs_keepnull? */

* Classification: query execution
* Severity: 2
* Complexity: 3
* Status: FIXME


## src/backend/executor/nodeIndexscan.c:982
	/* GPDB_91_MERGE_FIXME: why do we use op_lefttype here, when upstream uses op_righttype? */

* Classification:
* Severity: 
* Complexity: 
* Status: FIXME


## src/backend/executor/nodeModifyTable.c:800
	// GPDB_91_MERGE_FIXME: What if it's an AO table?

* Classification: query execution
* Severity: 3
* Complexity: 2 
* Status: FIXME


## src/backend/executor/nodeModifyTable.c:1788
	/*
	 * GPDB_91_MERGE_FIXME: we are largely just ignoring locking altogether.
	 * Perhaps that's OK as long as we take a full table lock on any UPDATEs
	 * or DELETEs. But sure doesn't seem right. Can we do better?
	 */

* Classification: query execution
* Severity: 3
* Complexity: 4
* Status: FIXME / TODO

We need to verify that we have a full table lock for this to become a TODO and
not a FIXME.


## src/backend/executor/nodeRowTrigger.c:125
	/* GPDB_91_MERGE_FIXME: should use TriggerEnabled here. */

* Classification: query execution
* Severity: 2
* Complexity: 2
* Status: FIXME

Seems like a reasonably straightforward fix.


## src/backend/gpopt/config/CConfigParamMapping.cpp:116
	// GPDB_91_MERGE_FIXME: I turned optimizer_minidump from bool into
	// an enum-type GUC. It's a bit dirty to cast it like this..

* Classification: ORCA
* Severity: 1
* Complexity: 1
* Status: FIXME


## src/backend/gpopt/config/CConfigParamMapping.cpp:419

	// GPDB_91_MERGE_FIXME - Disabling index scans under nested loop joins,
	// as the executor now expects a Param, rather than a Var with an OUTER
	// table reference for rescans.

* Classification: ORCA
* Severity: ?
* Complexity: ?
* Status: FIXME


## src/backend/gpopt/gpdbwrappers.cpp:1902 src/backend/gpopt/gpdbwrappers.cpp:2960 src/backend/gpopt/translate/CTranslatorDXLToPlStmt.cpp:1435
	// GPDB_91_MERGE_FIXME: collation
	// GPDB_91_MERGE_FIXME: collation
	// GPDB_91_MERGE_FIXME: collation

* Classification: ORCA
* Severity: ?
* Complexity: ?
* Status: FIXME


## src/backend/gpopt/translate/CTranslatorDXLToPlStmt.cpp:5077
	/* GPDB_91_MERGE_FIXME: what about unlogged? */

* Classification: ORCA
* Severity: ?
* Complexity: ?
* Status: FIXME


## src/backend/gpopt/translate/CTranslatorRelcacheToDXL.cpp:562
	// GPDB_91_MERGE_FIXME - Orca does not support foreign data

* Classification: ORCA
* Severity: ?
* Complexity: ?
* Status: FIXME


## src/backend/gpopt/translate/CTranslatorScalarToDXL.cpp:25
	/*
	 * GPDB_91_MERGE_FIXME: This allows us to call numeric_is_nan(). This is probably
	 * a violation of some ORCA coding rule, because we don't do this elsewhere...
	 */

* Classification: ORCA
* Severity: ?
* Complexity: ?
* Status: FIXME


## src/backend/nodes/nodeFuncs.c:885, 888, 891, 894, 897, 900, 903, 906 
	case T_DMLActionExpr:
		coll = InvalidOid;	/* GPDB_91_MERGE_FIXME: ORCA only expression */
		break;
	case T_PartSelectedExpr:
		coll = InvalidOid;	/* GPDB_91_MERGE_FIXME: ORCA only expression */
		break;
	case T_PartDefaultExpr:
		coll = InvalidOid;	/* GPDB_91_MERGE_FIXME: ORCA only expression */
		break;
	case T_PartBoundExpr:
		coll = InvalidOid;	/* GPDB_91_MERGE_FIXME: ORCA only expression */
		break;
	case T_PartBoundInclusionExpr:
		coll = InvalidOid;	/* GPDB_91_MERGE_FIXME: ORCA only expression */
		break;
	case T_PartBoundOpenExpr:
		coll = InvalidOid;	/* GPDB_91_MERGE_FIXME: ORCA only expression */
		break;
	case T_PartListRuleExpr:
		coll = InvalidOid;	/* GPDB_91_MERGE_FIXME: ORCA only expression */
		break;
	case T_PartListNullTestExpr:
		coll = InvalidOid;	/* GPDB_91_MERGE_FIXME: ORCA only expression */
		break;

* Classification: ORCA
* Severity: ?
* Complexity: ?
* Status: FIXME


## src/backend/optimizer/plan/plangroupext.c:451
	/*
	 * GPDB_91_MERGE_FIXME: The code in this file assumes that there is a
	 * one-to-one mapping between the pathkeys in root->group_pathkeys, and
	 * the grpColIdx array. However, that is not necessarily so. group_pathkeys
	 * is originally generated from root->parse->groupClause, just like the
	 * grpColIdx array. However, if there are unnecessary entries in
	 * group_pathkeys, the planner will eliminate them. For example, in this
	 * query (from the regression suite):
	 *
	 * select cn,sum(qty) from sale group by rollup(cn,vn) having vn=10;
	 *
	 * The 'vn' is a constant for any matching rows, therefore there is no
	 * need to sort on that column. The planner recognizes this, and
	 * eliminates it from group_pathkeys.
	 *
	 * To work around that problem, we negate any such optimization here, by
	 * reconstructing group_pathkeys with all the redundant columns present.
	 * That's pretty lame, but makes the tests pass.
	 */

* Classification: query planning
* Severity:
* Complexity:
* Status: TODO


## src/backend/optimizer/prep/prepjointree.c:638
	/*
	 * GPDB_91_MERGE_FIXME: The below enters an infinite recursion with
	 * this query from the 'qp_subquery' test:
	 *
	 * select Tbl04.* from Tbl04 where not ((Tbl04.a,Tbl04.b) in (select Tbl06.a,Tbl06.b from Tbl06) or (Tbl04.a,Tbl04.b) in (select i3.a, i3.b from i3)); -- expected: (5,6)
	 *
	 * That's because canonicalize_qual() was changed in 9.1, to not "push
	 * down" NOTs into an OR chain (commit 220e45bf32). I'm not sure if
	 * this is optimization is still worthwhile, and we should fix it,
	 * or we can just remove it. Commented it out for now, to avoid the
	 * crash.
	 */

* Classification: query planning
* Severity:
* Complexity:
* Status: TODO
 

## src/backend/optimizer/prep/preptlist.c:89
	/*
	 * GPDB_91_MERGE_FIXME:
	 * We seem to need to handle relkind other than RELKIND_RELATION.
	 * Previously there is no RELKIND_RELATION checking below, and that causes
	 * returning.sql test failure during pg 9.1 merging:
	 * "no relation entry for relid 2 (relnode.c:199)"
	 * That is because result_relation is the view relation while after
	 * we call makeVar() below, the code will finally call into
	 * add_vars_to_targetlist()->find_base_rel(), which accesses
	 * simple_rel_array[result_relation], however apparently
	 * simple_rel_array[] is not for a view relation.
	 * The key point is that after previous pullup, we should not use
	 * result_relation for varno in MakeVar(). It seems that we should move the
	 * code below to rewriteTargetListUD() and thus we could have
	 * the 'gp_segment_id' column with a correct varno in the Var.
	 */

* Classification: query planning
* Severity:
* Complexity:
* Status: TODO
 

## src/backend/parser/parse_partition.c:4683
	/*
	 * GPDB_91_MERGE_FIXME: For now, partition specifications always use
	 * default collation. We're not very consistent about that though, I think.
	 */

* Classification: query planning
* Severity:
* Complexity:
* Status: TODO

## src/backend/parser/parse_utilcmd.c:267
	/*
	 * GPDB_91_MERGE_FIXME: Previous gpdb does not allow create
	 * partition on temp table. Let's follow this at this moment
	 * although we do do not understand why even we know temp
	 * partition table seems to be not practical. Previous gpdb
	 * does not have this issue since in make_child_node()
	 * child_tab_name->istemp is not assigned and it's default
	 * value is false.
	 */

* Classification: query planning / parsing
* Severity: -
* Complexity: 3
* Status: TODO

Allowing partitions on temporary tables doesn't seem like a bad idea, but we
should think it through thoroughly before turning it on.


## src/backend/postmaster/autovacuum.c:2045
	/*
	 * GPDB_91_MERGE_FIXME: Autovacuum operates only on template0
	 * database in Greenplum.  We expect no temp tables in template0.
	 * Whenever we allow autovacuum to operate on user databases, we
	 * must deal with the logic to detect other backend's temp tables
	 * below.
	 */

* Classification: vacuum
* Severity: -
* Complexity: 4
* Status: TODO


## src/backend/utils/adt/lockfuncs.c:638
	/*
	 * GPDB_91_MERGE_FIXME: what to set these GPDB-specific fields to?
	 * These commented-out values are copy-pasted from the code above
	 * for normal locks.
	 */

* Classification: locking
* Severity: 2
* Complexity: 3
* Status: FIXME


## src/backend/utils/adt/numeric.c:4609
	/* GPDB_91_MERGE_FIXME: this always uses the "long" format. Is that good? */

* Classification: query execution
* Severity: 1
* Complexity: 3
* Status: FIXME

This should not lead to a risk of incorrect results, but may have performance
implications. Needs to be investigated if we should do anything or just leave
it as is.


## src/backend/utils/adt/pg_locale.c:1288
	/*
	 * GPDB_91_MERGE_FIXME: caching the value only makes sense for the default
	 * collation, but I think we incorrectly try to use it for everything.
	 * (Actually, strxfrm only works for the current LC_COLLATE setting, anyway.)
	 */

* Classification: query execution
* Severity: 3
* Complexity: 3
* Status: FIXME

We need to revisit if this actually works as intended, and while at it the
function should (if possible) be moved to the `tuplesort_mk` code to minimize
the impact on merging.


## src/backend/utils/adt/selfuncs.c:4923
	/*
	 * GPDB: GPDB allows users to modify pg_statistics.stavalues with
	 * UPDATEs (PostgreSQL complaints about the table row type not
	 * matching). So just in case that the type of the values in
	 * pg_statistics isn't what we'd expect, give an error rather than
	 * crash. That shouldn't happen, but better safe than sorry.
	 *
	 * GPDB_91_MERGE_FIXME: this is the second place we've added this. Does
	 * it need to be pulled into get_attstatsslot() itself?
	 */

* Classification: query execution
* Severity: -
* Complexity: 1
* Status: TODO

That would be a nice refactoring.


## src/backend/utils/adt/varlena.c:1491
	// GPDB_91_MERGE_FIXME: Why does gp_strcoll() exist?
	// Should we remove it, or add gp_strcoll_l()?

* Classification: query execution
* Severity: -
* Complexity: 2
* Status: FIXME

This needs to be investigated, the option to just remove it seems appealing as
the value of the function is.. questionable.


## src/backend/utils/adt/varlena.c:3134
	/* GPDB_91_MERGE_FIXME: We used to have modified implementation here
	 * before the 9.1 merge, that could handle large datums, larger than
	 * 250 MB, gracefully. Need to rewrite those changes over the new code
	 * here. */

* Classification: query execution
* Severity:3
* Complexity: 2
* Status: FIXME


## src/backend/utils/errcodes.txt:127
	# GPDB_91_MERGE_FIXME: Do we really need these GPDB-specific codes?
	# Can't we just use ERRCODE_FEATURE_NOT_SUPPORTED?

* Classification: query parsing
* Severity: 1
* Complexity: 1
* Status: FIXME

No we don't, yes we can.


## src/backend/parser/parse_clause.c
	# GPDB_91_MERGE_FIXME: Do we really need this code? Why ERROR_ prefix, instead of ERRCODE_ ?

* Classification: query parsing
* Severity: 1
* Complexity: 1
* Status: FIXME

No. We should use `ERRCODE_WINDOWING_ERROR`.


## src/backend/utils/sort/tuplesort_mk.c:745
	/* GPDB_91_MERGE_FIXME: these MKLV_TYPE_CHAR and MKLV_TYPE_TEXT
	 * fastpaths only work with the default collation of the database.
	 */

* Classification: query execution
* Severity:
* Complexity:
* Status: FIXME


## src/backend/utils/sort/tuplesort_mk.c:895 src/backend/utils/sort/tuplesort_mk.c:1713 src/include/utils/tuplesort.h:300
	elog(ERROR, "GPDB_91_MERGE_FIXME: tuplesort_begin_cluster_mk() not implemented yet");

	elog(ERROR, "GPDB_91_MERGE_FIXME: tuplesort_getheaptuple_mk not implemented yet");

	/* GPDB_91_MERGE_FIXME: The new 'cluster' variant hasn't been implemented
	 * in mksort yet.
	 */
* Classification: query execution
* Severity: 
* Complexity:
* Status: FIXME

If we are to keep tuplesort_mk it should have feature parity with the regular
tuplesort.


## src/backend/utils/time/snapmgr.c:195
	// GPDB_91_MERGE_FIXME: the name of UpdateSerializableCommandId is a bit
	// wrong, now that SERIALIZABLE and REPEATABLE READ are not the same.
	// From comparison, the if-check above was changed from checking
	// IsXactIsoLevelSerializable to IsolationUsesXactSnapshot()

* Classification: transaction isolation
* Severity: 1
* Complexity: 1
* Status: FIXME

Tidy up nomenclature.


## src/bin/pg_dump/pg_dump.c:8859
	/* GPDB_91_MERGE_FIXME: Where did this code come from? I can't see it in
	 * any upstream version, and I don't understand why we'd need it. Can
	 * we remove it?
	 */

* Classification: backup/restore
* Severity: 1
* Complexity: 1
* Status: FIXME

This should most likely be removed, but it needs to be validated.


## src/bin/psql/describe.c:1608
	/* GPDB_91_MERGE_FIXME: for an unlogged AO-table, we will not print
	 * the fact that it's unlogged */

* Classification: catalog/storage
* Severity: 1
* Complexity: 1
* Status: FIXME

Greenplum doesn't support unlogged AO/AOCS tables, so this comment can just be
removed it seems. We should validate that we nowhere accept an unlogged AO
though.


## src/bin/psql/help.c:236
	/* GPDB_91_MERGE_FIXME: can we use \dE for both external and foreign tables? */

* Classification: psql
* Severity: 1
* Complexity: 1
* Status: FIXME

Since external tables will be foreign tables shortly, they should be treated
equally in psql as a preparatory step.


## src/include/nodes/parsenodes.h:1685
	/* GPDB_91_MERGE_FIXME: why is this not a DistributedBy*? */

* Classification: query parsing
* Severity: 1
* Complexity: 1
* Status: FIXME

Very good question, we typecast it to DistributedBy when using it so it seems
quite silly to not declare it as such.


## src/include/storage/buf_internals.h:66
	/*
	 * Buffer tag identifies which disk block the buffer contains.
	 *
	 * Note: the BufferTag data must be sufficient to determine where to write the
	 * block, without reference to pg_class or pg_tablespace entries.  It's
	 * possible that the backend flushing the buffer doesn't even believe the
	 * relation is visible yet (its xact may have started before the xact that
	 * created the rel).  The storage manager must be able to cope anyway.
	 *
	 * GPDB_91_MERGE_FIXME: The argument in the previous note doesn't quite hold in
	 * GPDB.  Temp tables in GPDB use shared buffers.  But there is no way to
	 * distinguish a temp relation's buffers from a non-temp relation's buffers
	 * from buffer tag.  The flag BM_TEMP from buffer header is used to identify a
	 * temp relation's bufffers.
	 *
	 * Note: if there's any pad bytes in the struct, INIT_BUFFERTAG will have
	 * to be fixed to zero them, since this struct is used as a hash key.
	 */

* Classification: catalog/storage
* Severity:
* Complexity:
* Status: FIXME

Not sure what to do here, maybe rewording or extending the comment is all we
need to do.


## src/test/isolation/isolation_schedule:1
	# GPDB_91_MERGE_FIXME: GPDB acquires ExclusiveLock for update and delete SQL
	# statements.  This invalidates most of the isolation tests from upstream below.
	# E.g. an UPDATE step executed by isolationtester never returns because another
	# session run in the same permutation holds ExclusiveLock that blocks the step.

* Classification: test
* Severity: 1
* Complexity: 1
* Status: FIXME

Not really a FIXME, we just need to extend this comment a bit to make it clear
to readers why the schedule is commented out.


## src/test/isolation2/isolation2_main.c:106
	/*
	 * GPDB_91_MERGE_FIXME: pg_regress --launcher argument was added in PostgreSQL 9.1.
	 * We don't have it in GPDB yet. Re-enable this when we merge with 9.1.
	 */

* Classification: test
* Severity: 1
* Complexity: 1 
* Status: FIXME

Just need to remove the `#ifdef` here.


## src/test/regress/expected/bfv_partition_plans.out:123 src/test/regress/expected/bfv_partition_plans_optimizer.out:122 src/test/regress/expected/gporca_optimizer.out:10018
	-- GPDB_91_MERGE_FIXME GPORCA will no longer generate IndexedNLJ
	-- GPDB_91_MERGE_FIXME GPORCA will no longer generate IndexedNLJ
	-- GPDB_91_MERGE_FIXME GPORCA will fall back since we no longer generate IndexedNLJ and this test disable all other alternatives

* Classification: ORCA
* Severity:
* Complexity:
* Status: ?


## src/test/regress/expected/with.out:1393
	-- GPDB_91_MERGE_FIXME: MPP does not allow > 1 writer gang for one session while the
	-- case below (writable CTE introduced in pg 9.1) violates this. If we run the case we
	-- will see error as below.
	-- ERROR:  INSERT/UPDATE/DELETE must be executed by a writer segworker group: 2 0 (nodeModifyTable.c:1336)
	-- Besides writable CTE might need additional effort, so ignoring all of the cases (
	-- all are for writable CTE) since the first error introduces cascading errors.

* Classification: query execution / CTE
* Severity:
* Complexity:
* Status: 


## src/test/regress/input/dispatch.source:35
	-- GPDB_91_MERGE_FIXME: It would be good to leave the database in place, to
	-- also test gpcheckcat and pg_upgrade after all the regression tests have
	-- completed. As of this writing pg_upgrade does not in fact handle that well.
	-- Remove this DROP DATABASE once it's fixed. That should happen when we
	-- reach commit a2385cac13, which was backported to PostgreSQL 9.1.

* Classification: upgrade
* Severity: 3
* Complexity: 3
* Status: FIXME

`pg_upgrade` must be able to cope with all legal ways to name objects.
