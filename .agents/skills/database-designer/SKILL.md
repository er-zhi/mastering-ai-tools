# Database Designer - POWERFUL Tier Skill

## Overview

A comprehensive database design skill that provides expert-level analysis, optimization, and migration capabilities for modern database systems. This skill combines theoretical principles with practical tools to help architects and developers create scalable, performant, and maintainable database schemas.

## Core Competencies

### Schema Design & Analysis
- **Normalization Analysis**: Automated detection of normalization levels (1NF through BCNF)
- **Denormalization Strategy**: Smart recommendations for performance optimization
- **Data Type Optimization**: Identification of inappropriate types and size issues
- **Constraint Analysis**: Missing foreign keys, unique constraints, and null checks
- **Naming Convention Validation**: Consistent table and column naming patterns
- **ERD Generation**: Automatic Mermaid diagram creation from DDL

### Index Optimization
- **Index Gap Analysis**: Identification of missing indexes on foreign keys and query patterns
- **Composite Index Strategy**: Optimal column ordering for multi-column indexes
- **Index Redundancy Detection**: Elimination of overlapping and unused indexes
- **Performance Impact Modeling**: Selectivity estimation and query cost analysis
- **Index Type Selection**: B-tree, hash, partial, covering, and specialized indexes

### Migration Management
- **Zero-Downtime Migrations**: Expand-contract pattern implementation
- **Schema Evolution**: Safe column additions, deletions, and type changes
- **Data Migration Scripts**: Automated data transformation and validation
- **Rollback Strategy**: Complete reversal capabilities with validation
- **Execution Planning**: Ordered migration steps with dependency resolution

## Database Design Principles

### Schema Design
1. **Use meaningful names**: Clear, consistent naming conventions
2. **Choose appropriate data types**: Right-sized columns for storage efficiency
3. **Define proper constraints**: Foreign keys, check constraints, unique indexes
4. **Consider future growth**: Plan for scale from the beginning
5. **Document relationships**: Clear foreign key relationships and business rules

### Performance Optimization
1. **Index strategically**: Cover common query patterns without over-indexing
2. **Monitor query performance**: Regular analysis of slow queries
3. **Partition large tables**: Improve query performance and maintenance
4. **Use appropriate isolation levels**: Balance consistency with performance
5. **Implement connection pooling**: Efficient resource utilization

### Security Considerations
1. **Principle of least privilege**: Grant minimal necessary permissions
2. **Encrypt sensitive data**: At rest and in transit
3. **Audit access patterns**: Monitor and log database access
4. **Validate inputs**: Prevent SQL injection attacks
5. **Regular security updates**: Keep database software current

## Query Generation Patterns

### SELECT with JOINs

```sql
-- INNER JOIN: only matching rows
SELECT o.id, c.name, o.total
FROM orders o
INNER JOIN customers c ON c.id = o.customer_id;

-- LEFT JOIN: all left rows, NULLs for non-matches
SELECT c.name, COUNT(o.id) AS order_count
FROM customers c
LEFT JOIN orders o ON o.customer_id = c.id
GROUP BY c.name;
```

### Common Table Expressions (CTEs)

```sql
-- Recursive CTE for org chart
WITH RECURSIVE org AS (
  SELECT id, name, manager_id, 1 AS depth
  FROM employees WHERE manager_id IS NULL
  UNION ALL
  SELECT e.id, e.name, e.manager_id, o.depth + 1
  FROM employees e INNER JOIN org o ON o.id = e.manager_id
)
SELECT * FROM org ORDER BY depth, name;
```

### Window Functions

```sql
SELECT date, revenue,
  revenue - LAG(revenue) OVER (ORDER BY date) AS daily_change
FROM daily_sales;
```

## Migration Patterns

### Zero-Downtime Migrations (Expand/Contract)

1. **Expand** — add the new column/table (nullable, with default)
2. **Migrate data** — backfill in batches; dual-write from application
3. **Transition** — application reads from new column; stop writing to old
4. **Contract** — drop old column in a follow-up migration

## Performance Optimization

### Indexing Strategies

| Index Type | Use Case |
|------------|----------|
| **B-tree** (default) | Equality, range, ORDER BY |
| **GIN** | Full-text search, JSONB, arrays |
| **Partial** | Subset of rows (reduce size) |
| **Covering** | Index-only scans |

## Multi-Database Decision Matrix

| Criteria | PostgreSQL | MySQL | SQLite |
|----------|-----------|-------|--------|
| **Best for** | Complex queries, JSONB | Web apps, read-heavy | Embedded, dev/test |
| **JSON support** | Excellent (JSONB + GIN) | Good | Minimal |
| **Licensing** | Open source | GPL / commercial | Public domain |

**When to choose SQLite**: mobile apps, CLI tools, unit test databases, IoT/edge — matches current project stack.

## Cross-References

- **dependency-auditor** — security scanning for database drivers
- **architecture-patterns** — application-layer patterns and boundaries
