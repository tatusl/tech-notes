# Relational databases

* Tackling performance problems with more hardware usually just delays problems
* Instead of just provisioning bigger database instance, one should try to optimize database schema and queries
  * normalizing data is usually the first thing to do and check
    * normal forms
  * in schema design, one should avoid data duplication
* Primary keys can be also short strings instead of integers
  * with this, some information can be retained 
* PostgreSQL has auto compress feature (TOAST)

## Using EXPLAIN

* shows the query plan
* using `EXPLAIN ANALYZE` actually executes the statement and run time statistics are added to display

## Optimizing performance

* Sequential scans are usually bad for performance

### Indices

* To simplify, indices in tables turn sequential scans to index scans
* Most of the regularly used queries should have index
  * however the come with a cost
    * especially when writing to a table
* What if I have sequential scans even after adding an index
  * small table (not lots of rows)
  * on multi field indices, fields can be omitted only from the end
  * using function in where clause
    * however function can be used in index itself
* Postgres specific reasons why indices won't work
  * VACUUM hasn't been run, so table statistics are not updated

## Object-relational mappings (ORM)

* Usually it's a good thing to write simple code with abstractions
  * however, abstractions might have a performance cost
* Using ORM is usually recommend, but when performance is critical it could be better to write pure SQL

## Sharing responsibilities between application code and database

* Let the database do the heavy lifting
  * when database works, app sleeps
  * the is overhead going back and forth between application and database
* Prefer small number of big queries instead of big number of small queries

## Resources

* [PostgreSQL 14 Internals ebook](https://postgrespro.com/community/books/internals)
* [Postgres Weekly newsletter](https://postgresweekly.com/)

## References

* My own notes and interpretations on my colleagues excellent presentation with title "Your Customer Might Not Need a Bigger Database Instances" (17.01.2025)
