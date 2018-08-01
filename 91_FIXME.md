
## GNUmakefile.in:51
	#$(MAKE) -C contrib/file_fdw $@ # GPDB_91_MERGE_FIXME: disable installation until it's officially supported.

* Classification:
* Severity:
* Complexity:
* Status:


## contrib/file_fdw/file_fdw.c:780
    false); /* GPDB_91_MERGE_FIXME: do we need damping? */
	
* Classification:
* Severity:
* Complexity:
* Status:


## contrib/pg_upgrade/dump.c:27 contrib/pg_upgrade/dump.c:56
	/* GPDB_91_MERGE_FIXME "--quote-all-identifiers */ " --binary-upgrade %s -f %s",
	"\"%s/pg_dump\" %s --schema-only " /* GPDB_91_MERGE_FIXME --quote-all-identifiers " */

* Classification: upgrade
* Severity: 
* Complexity: 1
* Status: FIXME

This should just work now that we've merged with 9.1, PR submitted to fix.


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
* Severity:
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
* Severity:
* Complexity:
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

* Classification:
* Severity:
* Complexity:
* Status: FIXME


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


## src/backend/cdb/cdbcopy.c:623 src/include/cdb/cdbcopy.h:74
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

--
src/backend/commands/copy.c-953-				 errmsg("COPY single row error handling only available using COPY FROM")));
src/backend/commands/copy.c-954-
src/backend/commands/copy.c:955:/* GPDB_91_MERGE_FIXME: this should probably be done earlier, e.g. in parser */
src/backend/commands/copy.c-956-	/* Transfer any SREH options to the options list, so that BeginCopy can see them. */
src/backend/commands/copy.c-957-	if (stmt->sreh)
src/backend/commands/copy.c-958-	{
src/backend/commands/copy.c-959-		SingleRowErrorDesc *sreh = (SingleRowErrorDesc *) stmt->sreh;
src/backend/commands/copy.c-960-
--
src/backend/commands/copy.c-980-	}
src/backend/commands/copy.c-981-
src/backend/commands/copy.c:982:	/* GPDB_91_MERGE_FIXME: why not ? */
src/backend/commands/copy.c-983-	if (stmt->is_program && stmt->filename == NULL)
src/backend/commands/copy.c-984-		ereport(ERROR,
src/backend/commands/copy.c-985-				(errcode(ERRCODE_SYNTAX_ERROR),
src/backend/commands/copy.c-986-				 errmsg("STDIN/STDOUT not allowed with PROGRAM")));
src/backend/commands/copy.c-987-
--
src/backend/commands/copy.c-1040-		/* check read-only transaction */
src/backend/commands/copy.c-1041-		/*
src/backend/commands/copy.c:1042:		 * GPDB_91_MERGE_FIXME: is it possible to get to this point in the code
src/backend/commands/copy.c-1043-		 * with a temporary relation that belongs to another session? If so, the
src/backend/commands/copy.c-1044-		 * following code doesn't function as expected.
src/backend/commands/copy.c-1045-		 */
src/backend/commands/copy.c-1046-		if (XactReadOnly && rel->rd_backend != TempRelBackendId)
src/backend/commands/copy.c-1047-			PreventCommandIfReadOnly("COPY FROM");
--
src/backend/commands/copy.c-1125-	{
src/backend/commands/copy.c-1126-		/*
src/backend/commands/copy.c:1127:		 * GPDB_91_MERGE_FIXME: ExecutorStart() is called in BeginCopyTo,
src/backend/commands/copy.c-1128-		 * but the TRY-CATCH block only starts here. If an error is
src/backend/commands/copy.c-1129-		 * thrown in-between, we would fail to call mppExecutorCleanup. We
src/backend/commands/copy.c-1130-		 * really should be using a ResourceOwner or something else for
src/backend/commands/copy.c-1131-		 * cleanup, instead of TRY-CATCH blocks...
src/backend/commands/copy.c-1132-		 *
--
src/backend/commands/copy.c-1937-	int			i;
src/backend/commands/copy.c-1938-	uint64		processed = 0;
src/backend/commands/copy.c:1939:	int			rejected = 0; /* GPDB_91_MERGE_FIXME: should be uint64! */
src/backend/commands/copy.c-1940-
src/backend/commands/copy.c-1941-	dispatchStmt = copyObject((Node *) stmt);
src/backend/commands/copy.c-1942-
src/backend/commands/copy.c-1943-	/* add in partitions for dispatch */
src/backend/commands/copy.c-1944-	dispatchStmt->partitions = RelationBuildPartitionDesc(cstate->rel, false);
--
src/backend/commands/copy.c-1987-
src/backend/commands/copy.c-1988-	/*
src/backend/commands/copy.c:1989:	 * GPDB_91_MERGE_FIXME: SREH handling seems to be handled in a different
src/backend/commands/copy.c-1990-	 * place for every type of copy. This should be consolidated with the
src/backend/commands/copy.c-1991-	 * others.
src/backend/commands/copy.c-1992-	 */
src/backend/commands/copy.c-1993-	for (i = 0; i < pgresults.numResults; ++i)
src/backend/commands/copy.c-1994-	{
--
src/backend/commands/copy.c-2665-		{
src/backend/commands/copy.c-2666-			MemoryContextDelete(cstate->rowcontext);
src/backend/commands/copy.c:2667:			return 0; /* GPDB_91_MERGE_FIXME: does this make sense? */
src/backend/commands/copy.c-2668-		}
src/backend/commands/copy.c-2669-
src/backend/commands/copy.c-2670-		foreach(lc, target_rels)
src/backend/commands/copy.c-2671-		{
src/backend/commands/copy.c-2672-			Relation rel = lfirst(lc);
--
src/backend/commands/copy.c-4880-					 Datum *values, bool *nulls, Oid *tupleOid)
src/backend/commands/copy.c-4881-{
src/backend/commands/copy.c:4882:	/* GPDB_91_MERGE_FIXME: The idea here would be to only call the
src/backend/commands/copy.c-4883-	 * input function for the fields we need in the QD. But for now,
src/backend/commands/copy.c-4884-	 * screw performance. */
src/backend/commands/copy.c-4885-	return NextCopyFrom(cstate, econtext, values, nulls, tupleOid);
src/backend/commands/copy.c-4886-}
src/backend/commands/copy.c-4887-
--
src/backend/commands/copy.c-5175-
src/backend/commands/copy.c-5176-					/*
src/backend/commands/copy.c:5177:					 * GPDB_91_MERGE_FIXME: do we need to worry about toasted datums here?
src/backend/commands/copy.c-5178-					 * In theory, I suppose you could have a default value that gets evaluated
src/backend/commands/copy.c-5179-					 * in the master, and the default value comes from another table as a toast
src/backend/commands/copy.c-5180-					 * pointer.
src/backend/commands/copy.c-5181-					 */
src/backend/commands/copy.c-5182-					Assert (attr[attnum - 1]->attlen == -1);
--
src/backend/commands/copy.c-5533-				{
src/backend/commands/copy.c-5534-					/*
src/backend/commands/copy.c:5535:					 * GPDB_91_MERGE_FIXME: these commented-out blocks (as well
src/backend/commands/copy.c-5536-					 * as the restructured newline checks) are here because we
src/backend/commands/copy.c-5537-					 * allow the user to manually set the newline mode, and
src/backend/commands/copy.c-5538-					 * therefore don't error out on bare CR/LF in the middle of
src/backend/commands/copy.c-5539-					 * a column. Instead, they will be included verbatim.
src/backend/commands/copy.c-5540-					 *
--
src/backend/commands/copy.c-5566-				}
src/backend/commands/copy.c-5567-			}
src/backend/commands/copy.c:5568:#if 0 /* GPDB_91_MERGE_FIXME: see above. */
src/backend/commands/copy.c-5569-			else if (cstate->eol_type == EOL_NL)
src/backend/commands/copy.c-5570-				ereport(ERROR,
src/backend/commands/copy.c-5571-						(errcode(ERRCODE_BAD_COPY_FILE_FORMAT),
src/backend/commands/copy.c-5572-						 !cstate->csv_mode ?
src/backend/commands/copy.c-5573-						 errmsg("literal carriage return found in data") :
--
src/backend/commands/copy.c-5588-		if (c == '\n' && (!cstate->csv_mode || !in_quote))
src/backend/commands/copy.c-5589-		{
src/backend/commands/copy.c:5590:#if 0 /* GPDB_91_MERGE_FIXME: see above. */
src/backend/commands/copy.c-5591-			if (cstate->eol_type == EOL_CR || cstate->eol_type == EOL_CRNL)
src/backend/commands/copy.c-5592-				ereport(ERROR,
src/backend/commands/copy.c-5593-						(errcode(ERRCODE_BAD_COPY_FILE_FORMAT),
src/backend/commands/copy.c-5594-						 !cstate->csv_mode ?
src/backend/commands/copy.c-5595-						 errmsg("literal newline found in data") :



--
src/backend/cdb/cdbgroup.c-2100-									NIL, /* no havingQual */
src/backend/cdb/cdbgroup.c-2101-									aggstrategy,
src/backend/cdb/cdbgroup.c:2102:									/* GPDB_91_MERGE_FIXME: we used to pass
src/backend/cdb/cdbgroup.c-2103-									 * "ctx->agg_counts->numAggs - ctx->agg_counts->numOrderedAggs + 1"
src/backend/cdb/cdbgroup.c-2104-									 * as the "numAggs" argument. That argument is gone, make_agg
src/backend/cdb/cdbgroup.c-2105-									 * gets that number directly from agg_costs now. So this changed
src/backend/cdb/cdbgroup.c-2106-									 * the costing slightly. Maybe that's OK, or not?
src/backend/cdb/cdbgroup.c-2107-									 */
--
src/backend/cdb/cdbgroup.c-3346- * discover columns that need to be added back to the grouping target list.
src/backend/cdb/cdbgroup.c-3347- *
src/backend/cdb/cdbgroup.c:3348: * GPDB_91_MERGE_FIXME: This duplication is brittle. We should modify our
src/backend/cdb/cdbgroup.c-3349- * grouping planner to remove the assumptions on target list structure, and find
src/backend/cdb/cdbgroup.c-3350- * a better way to construct a correct series of Aggref stages.
src/backend/cdb/cdbgroup.c-3351- */
src/backend/cdb/cdbgroup.c-3352-static bool
src/backend/cdb/cdbgroup.c-3353-has_functional_dependency(Var *from, Var *to, List *rangeTable)
--
src/backend/cdb/cdbgroup.c-3595-
src/backend/cdb/cdbgroup.c-3596-	/*
src/backend/cdb/cdbgroup.c:3597:	 * GPDB_91_MERGE_FIXME: fix up the grps_tlist to also include any columns
src/backend/cdb/cdbgroup.c-3598-	 * that have a functional dependency on a primary key in the GROUP BY clause
src/backend/cdb/cdbgroup.c-3599-	 * (i.e. they are part of a relation that's already being grouped by a
src/backend/cdb/cdbgroup.c-3600-	 * primary key, which guarantees that tuples composed of these columns are
src/backend/cdb/cdbgroup.c-3601-	 * unique). We can avoid rewriting several assumptions by doing this, but
src/backend/cdb/cdbgroup.c-3602-	 * it's really ugly...
--
src/backend/cdb/cdbhash.c-781-		case TEXTOID:
src/backend/cdb/cdbhash.c-782-		/*
src/backend/cdb/cdbhash.c:783:		 * GPDB_91_MERGE_FIXME:
src/backend/cdb/cdbhash.c-784-		 * "pg_node_tree" is introduced in PG 9.1 to be
src/backend/cdb/cdbhash.c-785-		 * the type for nodeToString output. It can be
src/backend/cdb/cdbhash.c-786-		 * coerced to, but not from, text.
src/backend/cdb/cdbhash.c-787-		 *
src/backend/cdb/cdbhash.c-788-		 * Make it GPDB hashable here to let gpcheckcat
--
src/backend/cdb/cdbpartition.c-5130-																   RelationGetNamespace(rel)),
src/backend/cdb/cdbpartition.c-5131-												pstrdup(RelationGetRelationName(rel)), -1)),
src/backend/cdb/cdbpartition.c:5132:						RELPERSISTENCE_PERMANENT, /* GPDB_91_MERGE_FIXME: what if it's unlogged or temp? Where to get a proper value for this? */
src/backend/cdb/cdbpartition.c-5133-						true /* isPartitioned */ ,
src/backend/cdb/cdbpartition.c-5134-						&inheritOids, &old_constraints, &parentOidCount, NULL);
src/backend/cdb/cdbpartition.c-5135-
src/backend/cdb/cdbpartition.c-5136-	spec->partElem = list_make1(pelem);
src/backend/cdb/cdbpartition.c-5137-
--
src/backend/cdb/cdbvars.c-544-	Gp_role = newrole;
src/backend/cdb/cdbvars.c-545-
src/backend/cdb/cdbvars.c:546:	// GPDB_91_MERGE_FIXME: we don't have access to 'source' anymore
src/backend/cdb/cdbvars.c-547-	//if (source != PGC_S_DEFAULT)
src/backend/cdb/cdbvars.c-548-	{
src/backend/cdb/cdbvars.c-549-		if (do_connect)
src/backend/cdb/cdbvars.c-550-		{
src/backend/cdb/cdbvars.c-551-			/*
--
--
src/backend/commands/explain.c-601-
src/backend/commands/explain.c-602-		/*
src/backend/commands/explain.c:603:		 * GPDB_91_MERGE_FIXME: If the target is a partitioned table, we
src/backend/commands/explain.c-604-		 * should also report information on the triggers in the partitions.
src/backend/commands/explain.c-605-		 * I.e. we should scan the the 'ri_partition_hash' of each
src/backend/commands/explain.c-606-		 * ResultRelInfo as well. This is somewhat academic, though, as long
src/backend/commands/explain.c-607-		 * as we don't support triggers in GPDB in general..
src/backend/commands/explain.c-608-		 */
--
src/backend/commands/tablecmds.c-4040-	 * Phase 3: scan/rewrite tables as needed. If the data is in an external
src/backend/commands/tablecmds.c-4041-	 * table, no need to rewrite it or to add toast.
src/backend/commands/tablecmds.c:4042:	 * GPDB_91_MERGE_FIXME: How do we handle FOREIGN TABLEs? Should handle
src/backend/commands/tablecmds.c-4043-	 * external tables the same..
src/backend/commands/tablecmds.c-4044-	 */
src/backend/commands/tablecmds.c-4045-	if (!is_data_remote)
src/backend/commands/tablecmds.c-4046-	{
src/backend/commands/tablecmds.c-4047-		ATRewriteTables(&wqueue, lockmode);
--
src/backend/commands/variable.c-638-	XactIsoLevel = *((int *) extra);
src/backend/commands/variable.c-639-	/*
src/backend/commands/variable.c:640:	 * GPDB_91_MERGE_FIXME: Prior to PostgreSQL 9.1, serializable isolation was
src/backend/commands/variable.c-641-	 * implemented as read committed.  True serializable isolation level is
src/backend/commands/variable.c-642-	 * supported as of PostgreSQL 9.1.  However, Greenplum lacks the support to
src/backend/commands/variable.c-643-	 * detect serialization conflicts using predicate locks at cluster level.
src/backend/commands/variable.c-644-	 * Until that support is implemented, let's keep old behavior by falling
src/backend/commands/variable.c-645-	 * back to repeatable read. Also, for similar reasons guc
--
src/backend/executor/execMain.c-2159-		case RELKIND_VIEW:
src/backend/executor/execMain.c-2160-			/*
src/backend/executor/execMain.c:2161:			 * GPDB_91_MERGE_FIXME: In Greenplum, views are treated as non
src/backend/executor/execMain.c-2162-			 * partitioned relations, gp_distribution_policy contains no entry
src/backend/executor/execMain.c-2163-			 * for views.  Consequently, flow of a ModifyTable node for a view
src/backend/executor/execMain.c-2164-			 * is determined such that it is not dispatched to segments.
src/backend/executor/execMain.c-2165-			 * Things get confused if the DML statement has a where clause that
src/backend/executor/execMain.c-2166-			 * results in a direct dispatch to one segment.  Underlying scan
--
src/backend/executor/nodeDML.c-87-	node->cleanedUpSlot = ExecFilterJunk(node->junkfilter, projectedSlot);
src/backend/executor/nodeDML.c-88-
src/backend/executor/nodeDML.c:89:	/* GPDB_91_MERGE_FIXME:
src/backend/executor/nodeDML.c-90-	 * This kind of node is used by ORCA only. If in the future ORCA still uses
src/backend/executor/nodeDML.c-91-	 * DML node, canSetTag should be saved in DML plan node and init-ed by
src/backend/executor/nodeDML.c-92-	 * copying canSetTag value from the parse tree.
src/backend/executor/nodeDML.c-93-	 *
src/backend/executor/nodeDML.c-94-	 * For !isUpdate case, ExecInsert() and ExecDelete() should use canSetTag
--
src/backend/executor/nodeDML.c-120-				   NULL,
src/backend/executor/nodeDML.c-121-				   node->ps.state,
src/backend/executor/nodeDML.c:122:				   true, /* GPDB_91_MERGE_FIXME: canSetTag, where to get this? */
src/backend/executor/nodeDML.c-123-				   PLANGEN_OPTIMIZER /* Plan origin */,
src/backend/executor/nodeDML.c-124-				   isUpdate,
src/backend/executor/nodeDML.c-125-				   InvalidOid);
src/backend/executor/nodeDML.c-126-	}
src/backend/executor/nodeDML.c-127-	else /* DML_DELETE */
--
src/backend/executor/nodeDML.c-137-		/* Correct tuple count by ignoring deletes when splitting tuples. */
src/backend/executor/nodeDML.c-138-		ExecDelete(tupleid,
src/backend/executor/nodeDML.c:139:				   NULL, /* GPDB_91_MERGE_FIXME: oldTuple? */
src/backend/executor/nodeDML.c-140-				   node->cleanedUpSlot,
src/backend/executor/nodeDML.c-141-				   NULL /* DestReceiver */,
src/backend/executor/nodeDML.c-142-				   node->ps.state,
src/backend/executor/nodeDML.c:143:				   !isUpdate, /* GPDB_91_MERGE_FIXME: where to get canSetTag? */
src/backend/executor/nodeDML.c-144-				   PLANGEN_OPTIMIZER /* Plan origin */,
src/backend/executor/nodeDML.c-145-				   isUpdate);
src/backend/executor/nodeDML.c-146-	}
src/backend/executor/nodeDML.c-147-
src/backend/executor/nodeDML.c-148-	return slot;
--
src/backend/executor/nodeHashjoin.c-189-												node,
src/backend/executor/nodeHashjoin.c-190-												node->hj_HashOperators,
src/backend/executor/nodeHashjoin.c:191:				/* GPDB_91_MERGE_FIXME: do we need to get rid of hs_keepnull? */
src/backend/executor/nodeHashjoin.c-192-												HJ_FILL_INNER(node) || hashNode->hs_keepnull,
src/backend/executor/nodeHashjoin.c-193-												PlanStateOperatorMemKB((PlanState *) hashNode));
src/backend/executor/nodeHashjoin.c-194-				node->hj_HashTable = hashtable;
src/backend/executor/nodeHashjoin.c-195-
src/backend/executor/nodeHashjoin.c-196-				/*
--
src/backend/executor/nodeIndexscan.c-980-									   varattno,		/* attribute number */
src/backend/executor/nodeIndexscan.c-981-									   op_strategy,		/* op's strategy */
src/backend/executor/nodeIndexscan.c:982:									   /* GPDB_91_MERGE_FIXME: why do we use op_lefttype here, when upstream uses op_righttype? */
src/backend/executor/nodeIndexscan.c-983-									   op_lefttype,		/* strategy subtype */
src/backend/executor/nodeIndexscan.c-984-									   inputcollation,	/* collation */
src/backend/executor/nodeIndexscan.c-985-									   opfuncid,		/* reg proc to use */
src/backend/executor/nodeIndexscan.c-986-									   scanvalue);		/* constant */
src/backend/executor/nodeIndexscan.c-987-				n_sub_key++;
--
src/backend/executor/nodeModifyTable.c-798-		Buffer		delbuffer;
src/backend/executor/nodeModifyTable.c-799-
src/backend/executor/nodeModifyTable.c:800:		// GPDB_91_MERGE_FIXME: What if it's an AO table?
src/backend/executor/nodeModifyTable.c-801-
src/backend/executor/nodeModifyTable.c-802-		if (oldtuple != NULL)
src/backend/executor/nodeModifyTable.c-803-		{
src/backend/executor/nodeModifyTable.c-804-			deltuple.t_data = oldtuple;
src/backend/executor/nodeModifyTable.c-805-			deltuple.t_len = HeapTupleHeaderGetDatumLength(oldtuple);
--
src/backend/executor/nodeModifyTable.c-1786-		 */
src/backend/executor/nodeModifyTable.c-1787-		/*
src/backend/executor/nodeModifyTable.c:1788:		 * GPDB_91_MERGE_FIXME: we are largely just ignoring locking altogether.
src/backend/executor/nodeModifyTable.c-1789-		 * Perhaps that's OK as long as we take a full table lock on any UPDATEs
src/backend/executor/nodeModifyTable.c-1790-		 * or DELETEs. But sure doesn't seem right. Can we do better?
src/backend/executor/nodeModifyTable.c-1791-		 */
src/backend/executor/nodeModifyTable.c-1792-		{
src/backend/executor/nodeModifyTable.c-1793-			RangeTblEntry *rte = rt_fetch(rc->rti, estate->es_plannedstmt->rtable);
--
src/backend/executor/nodeRowTrigger.c-123-		Trigger    *trigger = &trigdesc->triggers[i];
src/backend/executor/nodeRowTrigger.c-124-
src/backend/executor/nodeRowTrigger.c:125:		/* GPDB_91_MERGE_FIXME: should use TriggerEnabled here. */
src/backend/executor/nodeRowTrigger.c-126-		if (!trigger->tgenabled)
src/backend/executor/nodeRowTrigger.c-127-		{
src/backend/executor/nodeRowTrigger.c-128-			continue;
src/backend/executor/nodeRowTrigger.c-129-		}
src/backend/executor/nodeRowTrigger.c-130-
--
src/backend/gpopt/config/CConfigParamMapping.cpp-114-		{
src/backend/gpopt/config/CConfigParamMapping.cpp-115-		EopttraceMinidump,
src/backend/gpopt/config/CConfigParamMapping.cpp:116:		// GPDB_91_MERGE_FIXME: I turned optimizer_minidump from bool into
src/backend/gpopt/config/CConfigParamMapping.cpp-117-		// an enum-type GUC. It's a bit dirty to cast it like this..
src/backend/gpopt/config/CConfigParamMapping.cpp-118-		(bool *) &optimizer_minidump,
src/backend/gpopt/config/CConfigParamMapping.cpp-119-		false, // m_fNegate
src/backend/gpopt/config/CConfigParamMapping.cpp-120-		GPOS_WSZ_LIT("Generate optimizer minidump.")
src/backend/gpopt/config/CConfigParamMapping.cpp-121-		},
--
src/backend/gpopt/config/CConfigParamMapping.cpp-417-
src/backend/gpopt/config/CConfigParamMapping.cpp-418-	// disable index-join if the corresponding GUC is turned off
src/backend/gpopt/config/CConfigParamMapping.cpp:419:	// GPDB_91_MERGE_FIXME - Disabling index scans under nested loop joins,
src/backend/gpopt/config/CConfigParamMapping.cpp-420-	// as the executor now expects a Param, rather than a Var with an OUTER
src/backend/gpopt/config/CConfigParamMapping.cpp-421-	// table reference for rescans.
src/backend/gpopt/config/CConfigParamMapping.cpp-422-	/*if (!optimizer_enable_indexjoin) */
src/backend/gpopt/config/CConfigParamMapping.cpp-423-	{
src/backend/gpopt/config/CConfigParamMapping.cpp-424-		CBitSet *pbsIndexJoin = CXform::PbsIndexJoinXforms(pmp);
--
src/backend/gpopt/gpdbwrappers.cpp-1900-	GP_WRAP_START;
src/backend/gpopt/gpdbwrappers.cpp-1901-	{
src/backend/gpopt/gpdbwrappers.cpp:1902:		// GPDB_91_MERGE_FIXME: collation
src/backend/gpopt/gpdbwrappers.cpp-1903-		Oid collation = OidTypeCollation(vartype);
src/backend/gpopt/gpdbwrappers.cpp-1904-		return makeVar(varno, varattno, vartype, vartypmod, collation, varlevelsup);
src/backend/gpopt/gpdbwrappers.cpp-1905-	}
src/backend/gpopt/gpdbwrappers.cpp-1906-	GP_WRAP_END;
src/backend/gpopt/gpdbwrappers.cpp-1907-	return NULL;
--
src/backend/gpopt/gpdbwrappers.cpp-2958-	GP_WRAP_START;
src/backend/gpopt/gpdbwrappers.cpp-2959-	{
src/backend/gpopt/gpdbwrappers.cpp:2960:		// GPDB_91_MERGE_FIXME: collation
src/backend/gpopt/gpdbwrappers.cpp-2961-		return evaluate_expr(pexpr, oidResultType, iTypeMod, InvalidOid);
src/backend/gpopt/gpdbwrappers.cpp-2962-	}
src/backend/gpopt/gpdbwrappers.cpp-2963-	GP_WRAP_END;
src/backend/gpopt/gpdbwrappers.cpp-2964-	return NULL;
src/backend/gpopt/gpdbwrappers.cpp-2965-}
--
src/backend/gpopt/translate/CTranslatorDXLToPlStmt.cpp-1433-		values_lists = gpdb::PlAppendElement(values_lists, value);
src/backend/gpopt/translate/CTranslatorDXLToPlStmt.cpp-1434-
src/backend/gpopt/translate/CTranslatorDXLToPlStmt.cpp:1435:		// GPDB_91_MERGE_FIXME: collation
src/backend/gpopt/translate/CTranslatorDXLToPlStmt.cpp-1436-		if (NIL == values_collations)
src/backend/gpopt/translate/CTranslatorDXLToPlStmt.cpp-1437-		{
src/backend/gpopt/translate/CTranslatorDXLToPlStmt.cpp-1438-			// Set collation based on the first list of values
src/backend/gpopt/translate/CTranslatorDXLToPlStmt.cpp-1439-			for (ULONG ulCol = 0; ulCol < ulCols ; ulCol++)
src/backend/gpopt/translate/CTranslatorDXLToPlStmt.cpp-1440-			{
--
src/backend/gpopt/translate/CTranslatorDXLToPlStmt.cpp-5075-	IntoClause *pintocl = MakeNode(IntoClause);
src/backend/gpopt/translate/CTranslatorDXLToPlStmt.cpp-5076-	pintocl->rel = MakeNode(RangeVar);
src/backend/gpopt/translate/CTranslatorDXLToPlStmt.cpp:5077:	/* GPDB_91_MERGE_FIXME: what about unlogged? */
src/backend/gpopt/translate/CTranslatorDXLToPlStmt.cpp-5078-	pintocl->rel->relpersistence = pdxlop->FTemporary() ? RELPERSISTENCE_TEMP : RELPERSISTENCE_PERMANENT;
src/backend/gpopt/translate/CTranslatorDXLToPlStmt.cpp-5079-	pintocl->rel->relname = CTranslatorUtils::SzFromWsz(pdxlop->Pmdname()->Pstr()->Wsz());
src/backend/gpopt/translate/CTranslatorDXLToPlStmt.cpp-5080-	pintocl->rel->schemaname = NULL;
src/backend/gpopt/translate/CTranslatorDXLToPlStmt.cpp-5081-	if (NULL != pdxlop->PmdnameSchema())
src/backend/gpopt/translate/CTranslatorDXLToPlStmt.cpp-5082-	{
--
src/backend/gpopt/translate/CTranslatorRelcacheToDXL.cpp-560-	}
src/backend/gpopt/translate/CTranslatorRelcacheToDXL.cpp-561-
src/backend/gpopt/translate/CTranslatorRelcacheToDXL.cpp:562:	// GPDB_91_MERGE_FIXME - Orca does not support foreign data
src/backend/gpopt/translate/CTranslatorRelcacheToDXL.cpp-563-	if (RelationIsForeign(rel))
src/backend/gpopt/translate/CTranslatorRelcacheToDXL.cpp-564-	{
src/backend/gpopt/translate/CTranslatorRelcacheToDXL.cpp-565-		GPOS_RAISE(gpdxl::ExmaMD, gpdxl::ExmiMDObjUnsupported, GPOS_WSZ_LIT("Foreign Data"));
src/backend/gpopt/translate/CTranslatorRelcacheToDXL.cpp-566-	}
src/backend/gpopt/translate/CTranslatorRelcacheToDXL.cpp-567-
--
src/backend/gpopt/translate/CTranslatorScalarToDXL.cpp-23-
src/backend/gpopt/translate/CTranslatorScalarToDXL.cpp-24-/*
src/backend/gpopt/translate/CTranslatorScalarToDXL.cpp:25: * GPDB_91_MERGE_FIXME: This allows us to call numeric_is_nan(). This is probably
src/backend/gpopt/translate/CTranslatorScalarToDXL.cpp-26- * a violation of some ORCA coding rule, because we don't do this elsewhere...
src/backend/gpopt/translate/CTranslatorScalarToDXL.cpp-27- */
src/backend/gpopt/translate/CTranslatorScalarToDXL.cpp-28-extern "C" {
src/backend/gpopt/translate/CTranslatorScalarToDXL.cpp-29-#include "utils/numeric.h"
src/backend/gpopt/translate/CTranslatorScalarToDXL.cpp-30-}
--
src/backend/nodes/nodeFuncs.c-883-
src/backend/nodes/nodeFuncs.c-884-		case T_DMLActionExpr:
src/backend/nodes/nodeFuncs.c:885:			coll = InvalidOid;	/* GPDB_91_MERGE_FIXME: ORCA only expression */
src/backend/nodes/nodeFuncs.c-886-			break;
src/backend/nodes/nodeFuncs.c-887-		case T_PartSelectedExpr:
src/backend/nodes/nodeFuncs.c:888:			coll = InvalidOid;	/* GPDB_91_MERGE_FIXME: ORCA only expression */
src/backend/nodes/nodeFuncs.c-889-			break;
src/backend/nodes/nodeFuncs.c-890-		case T_PartDefaultExpr:
src/backend/nodes/nodeFuncs.c:891:			coll = InvalidOid;	/* GPDB_91_MERGE_FIXME: ORCA only expression */
src/backend/nodes/nodeFuncs.c-892-			break;
src/backend/nodes/nodeFuncs.c-893-		case T_PartBoundExpr:
src/backend/nodes/nodeFuncs.c:894:			coll = InvalidOid;	/* GPDB_91_MERGE_FIXME: ORCA only expression */
src/backend/nodes/nodeFuncs.c-895-			break;
src/backend/nodes/nodeFuncs.c-896-		case T_PartBoundInclusionExpr:
src/backend/nodes/nodeFuncs.c:897:			coll = InvalidOid;	/* GPDB_91_MERGE_FIXME: ORCA only expression */
src/backend/nodes/nodeFuncs.c-898-			break;
src/backend/nodes/nodeFuncs.c-899-		case T_PartBoundOpenExpr:
src/backend/nodes/nodeFuncs.c:900:			coll = InvalidOid;	/* GPDB_91_MERGE_FIXME: ORCA only expression */
src/backend/nodes/nodeFuncs.c-901-			break;
src/backend/nodes/nodeFuncs.c-902-		case T_PartListRuleExpr:
src/backend/nodes/nodeFuncs.c:903:			coll = InvalidOid;	/* GPDB_91_MERGE_FIXME: ORCA only expression */
src/backend/nodes/nodeFuncs.c-904-			break;
src/backend/nodes/nodeFuncs.c-905-		case T_PartListNullTestExpr:
src/backend/nodes/nodeFuncs.c:906:			coll = InvalidOid;	/* GPDB_91_MERGE_FIXME: ORCA only expression */
src/backend/nodes/nodeFuncs.c-907-			break;
src/backend/nodes/nodeFuncs.c-908-		default:
src/backend/nodes/nodeFuncs.c-909-			elog(ERROR, "unrecognized node type: %d", (int) nodeTag(expr));
src/backend/nodes/nodeFuncs.c-910-			coll = InvalidOid;	/* keep compiler quiet */
src/backend/nodes/nodeFuncs.c-911-			break;
--
src/backend/optimizer/plan/plangroupext.c-449-
src/backend/optimizer/plan/plangroupext.c-450-	/*
src/backend/optimizer/plan/plangroupext.c:451:	 * GPDB_91_MERGE_FIXME: The code in this file assumes that there is a
src/backend/optimizer/plan/plangroupext.c-452-	 * one-to-one mapping between the pathkeys in root->group_pathkeys, and
src/backend/optimizer/plan/plangroupext.c-453-	 * the grpColIdx array. However, that is not necessarily so. group_pathkeys
src/backend/optimizer/plan/plangroupext.c-454-	 * is originally generated from root->parse->groupClause, just like the
src/backend/optimizer/plan/plangroupext.c-455-	 * grpColIdx array. However, if there are unnecessary entries in
src/backend/optimizer/plan/plangroupext.c-456-	 * group_pathkeys, the planner will eliminate them. For example, in this
--
src/backend/optimizer/prep/prepjointree.c-636-		}
src/backend/optimizer/prep/prepjointree.c-637-		/*
src/backend/optimizer/prep/prepjointree.c:638:		 * GPDB_91_MERGE_FIXME: The below enters an infinite recursion with
src/backend/optimizer/prep/prepjointree.c-639-		 * this query from the 'qp_subquery' test:
src/backend/optimizer/prep/prepjointree.c-640-		 *
src/backend/optimizer/prep/prepjointree.c-641-		 * select Tbl04.* from Tbl04 where not ((Tbl04.a,Tbl04.b) in (select Tbl06.a,Tbl06.b from Tbl06) or (Tbl04.a,Tbl04.b) in (select i3.a, i3.b from i3)); -- expected: (5,6)
src/backend/optimizer/prep/prepjointree.c-642-		 *
src/backend/optimizer/prep/prepjointree.c-643-		 * That's because canonicalize_qual() was changed in 9.1, to not "push
--
src/backend/optimizer/prep/preptlist.c-87-
src/backend/optimizer/prep/preptlist.c-88-	/*
src/backend/optimizer/prep/preptlist.c:89:	 * GPDB_91_MERGE_FIXME:
src/backend/optimizer/prep/preptlist.c-90-	 * We seem to need to handle relkind other than RELKIND_RELATION.
src/backend/optimizer/prep/preptlist.c-91-	 * Previously there is no RELKIND_RELATION checking below, and that causes
src/backend/optimizer/prep/preptlist.c-92-	 * returning.sql test failure during pg 9.1 merging:
src/backend/optimizer/prep/preptlist.c-93-	 * "no relation entry for relid 2 (relnode.c:199)"
src/backend/optimizer/prep/preptlist.c-94-	 * That is because result_relation is the view relation while after
--
src/backend/parser/parse_partition.c-4681-
src/backend/parser/parse_partition.c-4682-	/*
src/backend/parser/parse_partition.c:4683:	 * GPDB_91_MERGE_FIXME: For now, partition specifications always use
src/backend/parser/parse_partition.c-4684-	 * default collation. We're not very consistent about that though, I think.
src/backend/parser/parse_partition.c-4685-	 */
src/backend/parser/parse_partition.c-4686-	typcollation = get_typcollation(typid);
src/backend/parser/parse_partition.c-4687-
src/backend/parser/parse_partition.c-4688-	/*
--
src/backend/parser/parse_utilcmd.c-265-
src/backend/parser/parse_utilcmd.c-266-	/*
src/backend/parser/parse_utilcmd.c:267:	 * GPDB_91_MERGE_FIXME: Previous gpdb does not allow create
src/backend/parser/parse_utilcmd.c-268-	 * partition on temp table. Let's follow this at this moment
src/backend/parser/parse_utilcmd.c-269-	 * although we do do not understand why even we know temp
src/backend/parser/parse_utilcmd.c-270-	 * partition table seems to be not practical. Previous gpdb
src/backend/parser/parse_utilcmd.c-271-	 * does not have this issue since in make_child_node()
src/backend/parser/parse_utilcmd.c-272-	 * child_tab_name->istemp is not assigned and it's default
--
src/backend/postmaster/autovacuum.c-2043-
src/backend/postmaster/autovacuum.c-2044-			/*
src/backend/postmaster/autovacuum.c:2045:			 * GPDB_91_MERGE_FIXME: Autovacuum operates only on template0
src/backend/postmaster/autovacuum.c-2046-			 * database in Greenplum.  We expect no temp tables in template0.
src/backend/postmaster/autovacuum.c-2047-			 * Whenever we allow autovacuum to operate on user databases, we
src/backend/postmaster/autovacuum.c-2048-			 * must deal with the logic to detect other backend's temp tables
src/backend/postmaster/autovacuum.c-2049-			 * below.
src/backend/postmaster/autovacuum.c-2050-			 */
--
src/backend/utils/adt/lockfuncs.c-636-
src/backend/utils/adt/lockfuncs.c-637-		/*
src/backend/utils/adt/lockfuncs.c:638:		 * GPDB_91_MERGE_FIXME: what to set these GPDB-specific fields to?
src/backend/utils/adt/lockfuncs.c-639-		 * These commented-out values are copy-pasted from the code above
src/backend/utils/adt/lockfuncs.c-640-		 * for normal locks.
src/backend/utils/adt/lockfuncs.c-641-		 */
src/backend/utils/adt/lockfuncs.c-642-		//values[14] = Int32GetDatum(proc->mppSessionId);
src/backend/utils/adt/lockfuncs.c-643-		//values[15] = BoolGetDatum(proc->mppIsWriter);
--
src/backend/utils/adt/numeric.c-4607-		return len;
src/backend/utils/adt/numeric.c-4608-
src/backend/utils/adt/numeric.c:4609:	/* GPDB_91_MERGE_FIXME: this always uses the "long" format. Is that good? */
src/backend/utils/adt/numeric.c-4610-	Assert(result);
src/backend/utils/adt/numeric.c-4611-	SET_VARSIZE(result, len);
src/backend/utils/adt/numeric.c-4612-	result->choice.n_long.n_sign_dscale =
src/backend/utils/adt/numeric.c-4613-		sign | (var->dscale & NUMERIC_DSCALE_MASK);
src/backend/utils/adt/numeric.c-4614-	result->choice.n_long.n_weight = weight;
--
src/backend/utils/adt/pg_locale.c-1286-
src/backend/utils/adt/pg_locale.c-1287-	/*
src/backend/utils/adt/pg_locale.c:1288:	 * GPDB_91_MERGE_FIXME: caching the value only makes sense for the default
src/backend/utils/adt/pg_locale.c-1289-	 * collation, but I think we incorrectly try to use it for everything.
src/backend/utils/adt/pg_locale.c-1290-	 * (Actually, strxfrm only works for the current LC_COLLATE setting, anyway.)
src/backend/utils/adt/pg_locale.c-1291-	 */
src/backend/utils/adt/pg_locale.c-1292-
src/backend/utils/adt/pg_locale.c-1293-	if ( scaleFactor == -1)
--
src/backend/utils/adt/selfuncs.c-4921-		 * crash. That shouldn't happen, but better safe than sorry.
src/backend/utils/adt/selfuncs.c-4922-		 *
src/backend/utils/adt/selfuncs.c:4923:		 * GPDB_91_MERGE_FIXME: this is the second place we've added this. Does
src/backend/utils/adt/selfuncs.c-4924-		 * it need to be pulled into get_attstatsslot() itself?
src/backend/utils/adt/selfuncs.c-4925-		 */
src/backend/utils/adt/selfuncs.c-4926-		if (!IsBinaryCoercible(sslot.valuetype, vardata->atttype))
src/backend/utils/adt/selfuncs.c-4927-			elog(ERROR, "invalid histogram of type %s, for attribute of type %s",
src/backend/utils/adt/selfuncs.c-4928-				 format_type_be(sslot.valuetype), format_type_be(vardata->atttype));
--
src/backend/utils/adt/varlena.c-1489-		a2p[len2] = '\0';
src/backend/utils/adt/varlena.c-1490-
src/backend/utils/adt/varlena.c:1491:		// GPDB_91_MERGE_FIXME: Why does gp_strcoll() exist?
src/backend/utils/adt/varlena.c-1492-		// Should we remove it, or add gp_strcoll_l()?
src/backend/utils/adt/varlena.c-1493-#ifdef HAVE_LOCALE_T
src/backend/utils/adt/varlena.c-1494-		if (mylocale)
src/backend/utils/adt/varlena.c-1495-			result = strcoll_l(a1p, a2p, mylocale);
src/backend/utils/adt/varlena.c-1496-		else
--
src/backend/utils/adt/varlena.c-3132-}
src/backend/utils/adt/varlena.c-3133-
src/backend/utils/adt/varlena.c:3134:/* GPDB_91_MERGE_FIXME: We used to have modified implementation here
src/backend/utils/adt/varlena.c-3135- * before the 9.1 merge, that could handle large datums, larger than
src/backend/utils/adt/varlena.c-3136- * 250 MB, gracefully. Need to rewrite those changes over the new code
src/backend/utils/adt/varlena.c-3137- * here. */
src/backend/utils/adt/varlena.c-3138-
src/backend/utils/adt/varlena.c-3139-
--
src/backend/utils/cache/relcache.c-2730-	 * value as relfilenode in future.  E.g. if this is a non-temp relation and
src/backend/utils/cache/relcache.c-2731-	 * the future relation happens to be a temp relation.  Shared buffer
src/backend/utils/cache/relcache.c:2732:	 * manager in Greenplum breaks if this happens, see GPDB_91_MERGE_FIXME in
src/backend/utils/cache/relcache.c-2733-	 * GetNewRelFileNode() for details.
src/backend/utils/cache/relcache.c-2734-	 */
src/backend/utils/cache/relcache.c-2735-	if (relid < FirstNormalObjectId) /* bootstrap only */
src/backend/utils/cache/relcache.c-2736-		rel->rd_rel->relfilenode = relid;
src/backend/utils/cache/relcache.c-2737-	else
--
src/backend/utils/errcodes.txt-125-
src/backend/utils/errcodes.txt-126-0A000    E    ERRCODE_FEATURE_NOT_SUPPORTED                                  feature_not_supported
src/backend/utils/errcodes.txt:127:# GPDB_91_MERGE_FIXME: Do we really need these GPDB-specific codes?
src/backend/utils/errcodes.txt-128-# Can't we just use ERRCODE_FEATURE_NOT_SUPPORTED?
src/backend/utils/errcodes.txt-129-0AM00    E    ERRCODE_GP_FEATURE_NOT_SUPPORTED                               gp_feature_not_supported
src/backend/utils/errcodes.txt-130-0AM01    E    ERRCODE_GP_FEATURE_NOT_YET                                     gp_feature_not_yet
src/backend/utils/errcodes.txt-131-0AM02    E    ERRCODE_GP_FEATURE_NOT_CONFIGURED                              gp_feature_not_configured
src/backend/utils/errcodes.txt-132-
--
src/backend/utils/errcodes.txt-217-2200S    E    ERRCODE_INVALID_XML_COMMENT                                    invalid_xml_comment
src/backend/utils/errcodes.txt-218-2200T    E    ERRCODE_INVALID_XML_PROCESSING_INSTRUCTION                     invalid_xml_processing_instruction
src/backend/utils/errcodes.txt:219:# GPDB_91_MERGE_FIXME: Do we really need this code? Why ERROR_ prefix, instead of ERRCODE_ ?
src/backend/utils/errcodes.txt-220-22013    E    ERROR_INVALID_WINDOW_FRAME_PARAMETER                           invalid_window_frame_parameter
src/backend/utils/errcodes.txt-221-22M01    E    ERRCODE_NO_PARTITION_FOR_PARTITIONING_KEY                      no_partition_for_partitioning_key
src/backend/utils/errcodes.txt-222-
src/backend/utils/errcodes.txt-223-Section: Class 23 - Integrity Constraint Violation
src/backend/utils/errcodes.txt-224-
--
src/backend/utils/sort/tuplesort_mk.c-743-				sinfo->lvtype = MKLV_TYPE_INT32;
src/backend/utils/sort/tuplesort_mk.c-744-
src/backend/utils/sort/tuplesort_mk.c:745:			/* GPDB_91_MERGE_FIXME: these MKLV_TYPE_CHAR and MKLV_TYPE_TEXT
src/backend/utils/sort/tuplesort_mk.c-746-			 * fastpaths only work with the default collation of the database.
src/backend/utils/sort/tuplesort_mk.c-747-			 */
src/backend/utils/sort/tuplesort_mk.c-748-			if (!lc_collate_is_c(sinfo->scanKey.sk_collation) &&
src/backend/utils/sort/tuplesort_mk.c-749-				sinfo->scanKey.sk_collation == DEFAULT_COLLATION_OID)
src/backend/utils/sort/tuplesort_mk.c-750-			{
--
src/backend/utils/sort/tuplesort_mk.c-893-						   int workMem, bool randomAccess)
src/backend/utils/sort/tuplesort_mk.c-894-{
src/backend/utils/sort/tuplesort_mk.c:895:	elog(ERROR, "GPDB_91_MERGE_FIXME: tuplesort_begin_cluster_mk() not implemented yet");
src/backend/utils/sort/tuplesort_mk.c-896-}
src/backend/utils/sort/tuplesort_mk.c-897-
src/backend/utils/sort/tuplesort_mk.c-898-Tuplesortstate_mk *
src/backend/utils/sort/tuplesort_mk.c-899-tuplesort_begin_index_mk(Relation indexRel,
src/backend/utils/sort/tuplesort_mk.c-900-						 bool enforceUnique,
--
src/backend/utils/sort/tuplesort_mk.c-1711-{
src/backend/utils/sort/tuplesort_mk.c-1712-	// Need to extract a HeapTuple from here somehow...
src/backend/utils/sort/tuplesort_mk.c:1713:	elog(ERROR, "GPDB_91_MERGE_FIXME: tuplesort_getheaptuple_mk not implemented yet");
src/backend/utils/sort/tuplesort_mk.c-1714-}
src/backend/utils/sort/tuplesort_mk.c-1715-
src/backend/utils/sort/tuplesort_mk.c-1716-/*
src/backend/utils/sort/tuplesort_mk.c-1717- * Fetch the next index tuple in either forward or back direction.
src/backend/utils/sort/tuplesort_mk.c-1718- * Returns NULL if no more tuples.  If *should_free is set, the
--
src/backend/utils/time/snapmgr.c-193-			 DtxContextToString(DistributedTransactionContext));
src/backend/utils/time/snapmgr.c-194-
src/backend/utils/time/snapmgr.c:195:		// GPDB_91_MERGE_FIXME: the name of UpdateSerializableCommandId is a bit
src/backend/utils/time/snapmgr.c-196-		// wrong, now that SERIALIZABLE and REPEATABLE READ are not the same.
src/backend/utils/time/snapmgr.c-197-		// From comparison, the if-check above was changed from checking
src/backend/utils/time/snapmgr.c-198-		// IsXactIsoLevelSerializable to IsolationUsesXactSnapshot()
src/backend/utils/time/snapmgr.c-199-		UpdateSerializableCommandId(CurrentSnapshot->curcid);
src/backend/utils/time/snapmgr.c-200-
--
src/bin/pg_dump/pg_dump.c-8857-	appendPQExpBuffer(defqry, ";\n");
src/bin/pg_dump/pg_dump.c-8858-
src/bin/pg_dump/pg_dump.c:8859:	/* GPDB_91_MERGE_FIXME: Where did this code come from? I can't see it in
src/bin/pg_dump/pg_dump.c-8860-	 * any upstream version, and I don't understand why we'd need it. Can
src/bin/pg_dump/pg_dump.c-8861-	 * we remove it?
src/bin/pg_dump/pg_dump.c-8862-	 */
src/bin/pg_dump/pg_dump.c-8863-	/*
src/bin/pg_dump/pg_dump.c-8864-	 * If the language is one of those for which the call handler and
--
src/bin/psql/describe.c-1606-	{
src/bin/psql/describe.c-1607-		case 'r':
src/bin/psql/describe.c:1608:			/* GPDB_91_MERGE_FIXME: for an unlogged AO-table, we will not print
src/bin/psql/describe.c-1609-			 * the fact that it's unlogged */
src/bin/psql/describe.c-1610-			if(tableinfo.relstorage == 'a')
src/bin/psql/describe.c-1611-				printfPQExpBuffer(&title, _("Append-Only Table \"%s.%s\""),
src/bin/psql/describe.c-1612-								  schemaname, relationname);
src/bin/psql/describe.c-1613-			else if(tableinfo.relstorage == 'c')
--
src/bin/psql/help.c-234-	fprintf(output, _("  \\du[+]  [PATTERN]      list roles\n"));
src/bin/psql/help.c-235-	fprintf(output, _("  \\dv[S+] [PATTERN]      list views\n"));
src/bin/psql/help.c:236:	/* GPDB_91_MERGE_FIXME: can we use \dE for both external and foreign tables? */
src/bin/psql/help.c-237-	fprintf(output, _("  \\dE     [PATTERN]      list external tables\n"));
src/bin/psql/help.c-238-	fprintf(output, _("  \\dE[S+] [PATTERN]      list foreign tables\n"));
src/bin/psql/help.c-239-	fprintf(output, _("  \\dx[+]  [PATTERN]      list extensions\n"));
src/bin/psql/help.c-240-	fprintf(output, _("  \\l[+]                  list all databases\n"));
src/bin/psql/help.c-241-	fprintf(output, _("  \\sf[+] FUNCNAME        show a function's definition\n"));
--
src/include/nodes/parsenodes.h-1683-	bool		if_not_exists;	/* just do nothing if it already exists? */
src/include/nodes/parsenodes.h-1684-
src/include/nodes/parsenodes.h:1685:	/* GPDB_91_MERGE_FIXME: why is this not a DistributedBy*? */
src/include/nodes/parsenodes.h-1686-	Node       *distributedBy;   /* what columns we distribute the data by */
src/include/nodes/parsenodes.h-1687-	Node       *partitionBy;     /* what columns we partition the data by */
src/include/nodes/parsenodes.h-1688-	char	    relKind;         /* CDB: force relkind to this */
src/include/nodes/parsenodes.h-1689-	char		relStorage;
src/include/nodes/parsenodes.h-1690-	GpPolicy   *policy;
--
src/include/storage/buf_internals.h-64- * created the rel).  The storage manager must be able to cope anyway.
src/include/storage/buf_internals.h-65- *
src/include/storage/buf_internals.h:66: * GPDB_91_MERGE_FIXME: The argument in the previous note doesn't quite hold in
src/include/storage/buf_internals.h-67- * GPDB.  Temp tables in GPDB use shared buffers.  But there is no way to
src/include/storage/buf_internals.h-68- * distinguish a temp relation's buffers from a non-temp relation's buffers
src/include/storage/buf_internals.h-69- * from buffer tag.  The flag BM_TEMP from buffer header is used to identify a
src/include/storage/buf_internals.h-70- * temp relation's bufffers.
src/include/storage/buf_internals.h-71- *
--
src/include/utils/tuplesort.h-298-	switcheroo_Tuplesortstate *state;
src/include/utils/tuplesort.h-299-
src/include/utils/tuplesort.h:300:	/* GPDB_91_MERGE_FIXME: The new 'cluster' variant hasn't been implemented
src/include/utils/tuplesort.h-301-	 * in mksort yet.
src/include/utils/tuplesort.h-302-	 */
src/include/utils/tuplesort.h-303-#if 0
src/include/utils/tuplesort.h-304-	if (gp_enable_mk_sort)
src/include/utils/tuplesort.h-305-	{
--
src/test/isolation/isolation_schedule:1:# GPDB_91_MERGE_FIXME: GPDB acquires ExclusiveLock for update and delete SQL
src/test/isolation/isolation_schedule-2-# statements.  This invalidates most of the isolation tests from upstream below.
src/test/isolation/isolation_schedule-3-# E.g. an UPDATE step executed by isolationtester never returns because another
src/test/isolation/isolation_schedule-4-# session run in the same permutation holds ExclusiveLock that blocks the step.
src/test/isolation/isolation_schedule-5-
src/test/isolation/isolation_schedule-6-#test: simple-write-skew
--
src/test/isolation2/isolation2_main.c-104-
src/test/isolation2/isolation2_main.c-105-	/*
src/test/isolation2/isolation2_main.c:106:	 * GPDB_91_MERGE_FIXME: pg_regress --launcher argument was added in PostgreSQL 9.1.
src/test/isolation2/isolation2_main.c-107-	 * We don't have it in GPDB yet. Re-enable this when we merge with 9.1.
src/test/isolation2/isolation2_main.c-108-	 */
src/test/isolation2/isolation2_main.c-109-#if PG_VERSION_NUM >= 90100
src/test/isolation2/isolation2_main.c-110-	if (launcher)
src/test/isolation2/isolation2_main.c-111-		offset += snprintf(psql_cmd + offset, sizeof(psql_cmd) - offset,
--
src/test/regress/expected/bfv_partition_plans.out-121--- TEST
src/test/regress/expected/bfv_partition_plans.out-122--- start_ignore
src/test/regress/expected/bfv_partition_plans.out:123:-- GPDB_91_MERGE_FIXME GPORCA will no longer generate IndexedNLJ
src/test/regress/expected/bfv_partition_plans.out-124--- end_ignore
src/test/regress/expected/bfv_partition_plans.out-125-select find_operator('select * from mpp23195_t1,mpp23195_t2 where mpp23195_t1.i < mpp23195_t2.i;', 'Dynamic Index Scan');
src/test/regress/expected/bfv_partition_plans.out-126- find_operator 
src/test/regress/expected/bfv_partition_plans.out-127----------------
src/test/regress/expected/bfv_partition_plans.out-128- ['false']
--
src/test/regress/expected/bfv_partition_plans_optimizer.out-120--- TEST
src/test/regress/expected/bfv_partition_plans_optimizer.out-121--- start_ignore
src/test/regress/expected/bfv_partition_plans_optimizer.out:122:-- GPDB_91_MERGE_FIXME GPORCA will no longer generate IndexedNLJ
src/test/regress/expected/bfv_partition_plans_optimizer.out-123--- end_ignore
src/test/regress/expected/bfv_partition_plans_optimizer.out-124-select find_operator('select * from mpp23195_t1,mpp23195_t2 where mpp23195_t1.i < mpp23195_t2.i;', 'Dynamic Index Scan');
src/test/regress/expected/bfv_partition_plans_optimizer.out-125- find_operator 
src/test/regress/expected/bfv_partition_plans_optimizer.out-126----------------
src/test/regress/expected/bfv_partition_plans_optimizer.out-127- ['false']
--
src/test/regress/expected/gporca_optimizer.out-10016-set optimizer_segments = 3;
src/test/regress/expected/gporca_optimizer.out-10017--- start_ignore
src/test/regress/expected/gporca_optimizer.out:10018:-- GPDB_91_MERGE_FIXME GPORCA will fall back since we no longer generate IndexedNLJ and this test disable all other alternatives
src/test/regress/expected/gporca_optimizer.out-10019--- end_ignore
src/test/regress/expected/gporca_optimizer.out-10020-EXPLAIN
src/test/regress/expected/gporca_optimizer.out-10021-SELECT (tt.event_ts / 100000) / 5 * 5 as fivemin, COUNT(*)
src/test/regress/expected/gporca_optimizer.out-10022-FROM my_tt_agg_opt tt, my_tq_agg_opt_part tq
src/test/regress/expected/gporca_optimizer.out-10023-WHERE tq.sym = tt.symbol AND
--
src/test/regress/expected/with.out-1391-
src/test/regress/expected/with.out-1392---start_ignore
src/test/regress/expected/with.out:1393:-- GPDB_91_MERGE_FIXME: MPP does not allow > 1 writer gang for one session while the
src/test/regress/expected/with.out-1394--- case below (writable CTE introduced in pg 9.1) violates this. If we run the case we
src/test/regress/expected/with.out-1395--- will see error as below.
src/test/regress/expected/with.out-1396--- ERROR:  INSERT/UPDATE/DELETE must be executed by a writer segworker group: 2 0 (nodeModifyTable.c:1336)
src/test/regress/expected/with.out-1397--- Besides writable CTE might need additional effort, so ignoring all of the cases (
src/test/regress/expected/with.out-1398--- all are for writable CTE) since the first error introduces cascading errors.
--
src/test/regress/expected/with.out-2021-DROP RULE y_rule ON y;
src/test/regress/expected/with.out-2022-
src/test/regress/expected/with.out:2023:-- Match previous start_ignore with GPDB_91_MERGE_FIXME
src/test/regress/expected/with.out-2024---end_ignore
--
src/test/regress/input/dispatch.source-33-ALTER DATABASE "funny""db'with\\quotes" SET search_path="funny""schema'with\\quotes";
src/test/regress/input/dispatch.source-34-
src/test/regress/input/dispatch.source:35:-- GPDB_91_MERGE_FIXME: It would be good to leave the database in place, to
src/test/regress/input/dispatch.source-36--- also test gpcheckcat and pg_upgrade after all the regression tests have
src/test/regress/input/dispatch.source-37--- completed. As of this writing pg_upgrade does not in fact handle that well.
src/test/regress/input/dispatch.source-38--- Remove this DROP DATABASE once it's fixed. That should happen when we
src/test/regress/input/dispatch.source-39--- reach commit a2385cac13, which was backported to PostgreSQL 9.1.
src/test/regress/input/dispatch.source-40-DROP DATABASE "funny""db'with\\quotes";
--
src/test/regress/output/dispatch.source-55-CREATE DATABASE "funny""db'with\\quotes";
src/test/regress/output/dispatch.source-56-ALTER DATABASE "funny""db'with\\quotes" SET search_path="funny""schema'with\\quotes";
src/test/regress/output/dispatch.source:57:-- GPDB_91_MERGE_FIXME: It would be good to leave the database in place, to
src/test/regress/output/dispatch.source-58--- also test gpcheckcat and pg_upgrade after all the regression tests have
src/test/regress/output/dispatch.source-59--- completed. As of this writing pg_upgrade does not in fact handle that well.
src/test/regress/output/dispatch.source-60--- Remove this DROP DATABASE once it's fixed. That should happen when we
src/test/regress/output/dispatch.source-61--- reach commit a2385cac13, which was backported to PostgreSQL 9.1.
src/test/regress/output/dispatch.source-62-DROP DATABASE "funny""db'with\\quotes";
--
src/test/regress/sql/bfv_partition_plans.sql-101--- TEST
src/test/regress/sql/bfv_partition_plans.sql-102--- start_ignore
src/test/regress/sql/bfv_partition_plans.sql:103:-- GPDB_91_MERGE_FIXME GPORCA will no longer generate IndexedNLJ
src/test/regress/sql/bfv_partition_plans.sql-104--- end_ignore
src/test/regress/sql/bfv_partition_plans.sql-105-select find_operator('select * from mpp23195_t1,mpp23195_t2 where mpp23195_t1.i < mpp23195_t2.i;', 'Dynamic Index Scan');
src/test/regress/sql/bfv_partition_plans.sql-106-select * from mpp23195_t1,mpp23195_t2 where mpp23195_t1.i < mpp23195_t2.i;
src/test/regress/sql/bfv_partition_plans.sql-107-
src/test/regress/sql/bfv_partition_plans.sql-108--- CLEANUP
--
src/test/regress/sql/gporca.sql-1565-set optimizer_segments = 3;
src/test/regress/sql/gporca.sql-1566--- start_ignore
src/test/regress/sql/gporca.sql:1567:-- GPDB_91_MERGE_FIXME GPORCA will fall back since we no longer generate IndexedNLJ and this test disable all other alternatives
src/test/regress/sql/gporca.sql-1568--- end_ignore
src/test/regress/sql/gporca.sql-1569-EXPLAIN
src/test/regress/sql/gporca.sql-1570-SELECT (tt.event_ts / 100000) / 5 * 5 as fivemin, COUNT(*)
src/test/regress/sql/gporca.sql-1571-FROM my_tt_agg_opt tt, my_tq_agg_opt_part tq
src/test/regress/sql/gporca.sql-1572-WHERE tq.sym = tt.symbol AND
--
src/test/regress/sql/with.sql-645-
src/test/regress/sql/with.sql-646---start_ignore
src/test/regress/sql/with.sql:647:-- GPDB_91_MERGE_FIXME: MPP does not allow > 1 writer gang for one session while the
src/test/regress/sql/with.sql-648--- case below (writable CTE introduced in pg 9.1) violates this. If we run the case we
src/test/regress/sql/with.sql-649--- will see error as below.
src/test/regress/sql/with.sql-650--- ERROR:  INSERT/UPDATE/DELETE must be executed by a writer segworker group: 2 0 (nodeModifyTable.c:1336)
src/test/regress/sql/with.sql-651--- Besides writable CTE might need additional effort, so ignoring all of the cases (
src/test/regress/sql/with.sql-652--- all are for writable CTE) since the first error introduces cascading errors.
--
src/test/regress/sql/with.sql-895-DROP RULE y_rule ON y;
src/test/regress/sql/with.sql-896-
src/test/regress/sql/with.sql:897:-- Match previous start_ignore with GPDB_91_MERGE_FIXME
src/test/regress/sql/with.sql-898---end_ignore
