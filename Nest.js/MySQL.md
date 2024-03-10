```SQL
CREATE TABLE IF NOT EXISTS `customers` (
	`id` int(11) NOT NULL AUTO_INCREMENT COMMENT 'Customer ID, auto-increment',
    `name` varchar(255) NOT NULL COMMENT 'Customer name, not empty',
    PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='Customer information form';

CREATE TABLE IF NOT EXISTS `orders` (
	`id` int(11) NOT NULL AUTO_INCREMENT COMMENT 'Order ID, auto-increment',
    `customer_id` int(11) NOT NULL COMMENT 'Customer ID, not empty',
    `order_date` date NOT NULL COMMENT 'Order date, not empty',
    `total_amount` decimal(10,2) NOT NULL COMMENT 'The total amount of the order, not empty',
    PRIMARY KEY(`id`),
    FOREIGN KEY(`customer_id`) REFERENCES `customers`(`id`) ON DELETE CASCADE ON UPDATE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='Order information table';

CREATE TABLE IF NOT EXISTS `order_items` (
	`id` int(11) NOT NULL AUTO_INCREMENT COMMENT 'Product ID, auto-increment',
    `order_id` int(11) NOT NULL COMMENT 'Order ID, not empty',
    `product_name` varchar(255) NOT NULL COMMENT 'Product Name, not empty',
    `quantity` int(11) NOT NULL COMMENT 'Product quantity, not empty',
	`price` decimal(10,2) NOT NULL COMMENT 'Product unit price, not empty',
	PRIMARY KEY (`id`),
    FOREIGN KEY (`order_id`) REFERENCES `orders` (`id`) ON DELETE CASCADE ON UPDATE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='Order product information table';
```

- `IF NOT EXISTS` 在建表前先检查数据库是否存在同名表，避免因重复建表而导致错误
- `orders` 表的 `customer_id` 引用 `customers` 表的 `id`字段，设置外键约束，确保 `customer_id` 的值在 `customers` 表的 `id` 字段中存在。`ON DELETE CASCADE ON UPDATE CASCADE` 表示若对应的 `customers` 表中的记录被删除或更新，相关联的 `orders` 表中的记录也会相应地被删除或更新

```SQL
SELECT customers.name, SUM(orders.total_amount) AS total_amount
    FROM customers
    JOIN orders ON customers.id = orders.customer_id
    GROUP BY customers.id
    ORDER BY total_amount DESC
    LIMIT 0,3;
```

- 选择要从查询结果中返回的列，`SUM(orders.total_amount)` 是一个聚合函数，用于计算每个顾客所有订单的总金额，结果列被命名为 `total_amount`
- 指定查询的主表是 `customers` 表
- 通过内连接将 `orders` 表和 `customers` 表连接起来。连接的条件是 `customers` 表的 `id` 字段和 `orders` 表的`customer_id` 字段相匹配
- 按 `customers.id` 对结果集进行分组
* 根据计算出的 `total_amount` 对结果集进行降序排序
* 从结果集的第一行开始，选择 3 行

```SQL
SELECT * FROM customers
	WHERE EXISTS (SELECT 1 FROM orders WHERE orders.customer_id = customers.id)
```

- 对于 `customers` 表中的每一行，数据库执行子查询，若找到至少一条 `orders` 表中的记录，其 `customer_id` 与当前 `customers` 表中行的 `id` 相匹配，那么子查询返回真，且当前的 `customers` 表中的行会被包含在最终的查询结果中，否则，当前的 `customers` 表中的行不会出现在查询结果中
- 在使用 `EXISTS` 时，子查询返回的具体数据列并不重要，只关心是否存在满足条件的行。因此，使用 `SELECT 1` 可提高查询效率，因为数据库无需处理额外的数据列
- 与直接使用 `JOIN` 相比，`EXISTS` 可在无需返回外表数据时提供更好的性能
