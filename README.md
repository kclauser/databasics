## Databasics

Fork this repository into your own github profile.

Before closing the homework assignment issue:

- Update this README as follows:
  - [ ] For each question (just after or below the question itself) include the SQL you wrote to generate the answer
  - [ ] For each question (just after or below the question itself) include the *answer* to the question asked.

- Also:
  - [ ] Commit the updated `store.sqlite3` database file
  - [ ] Include a link to your forked repo in the comments when closing the issue.


NOTE: To run sqlite with this database: `sqlite3 store.sqlite3`

NOTE: You may want to keep a backup of the `store.sqlite3` file in case you damage the file. *OR* you can use the command `git checkout store.sqlite3` to undo any changes to the database file since the fork or your last commit.

## Explorer Mode

- [√] How many users are there?
*50*
select * from users;
- [√] What are the 5 most expensive items?
*9341|Books & Outdoors|Ergonomic Steel Car
9390|Music & Baby|Sleek Wooden Hat
9790|Awesome Granite Pants
9859|Small Wooden Computer
9984|Small Cotton Gloves*
select "price", "title" from "items" order by "price";
- [√] What's the cheapest book? (Does that change for "category is exactly 'book'" versus "category contains 'book'"?)
*Ergonomic Granite Chair*
*no*
select * from "items" where category = 'Books' order by "price";
select * from "items" where category like '%Books%' order by "price";
- [√] Who lives at "6439 Zetta Hills, Willmouth, WY"? Do they have another address?
*Corrine Little*
SELECT "first_name", "last_name","street", "city", "state", "zip" FROM "users" AS C JOIN "addresses" AS A ON C.id=A.user_id WHERE "street" like "6439%";
*yes*
SELECT "first_name", "last_name","street", "city", "state", "zip" FROM "users" AS C JOIN "addresses" AS A ON C.id=A.user_id WHERE "last_name" = "Little";

- [√] Correct Virginie Mitchell's address to "New York, NY, 10108".
*to find addresses to correct, since sqlite doesnt support UPDATE on JOIN*
SELECT "first_name", "last_name","street", "city", "state", "zip" FROM "users" AS C JOIN "addresses" AS A ON C.id=A.user_id WHERE "last_name" = "Mitchell";
*this returns 2 addresses which I update using the following query with zip as the condition, it needs to be run twice*
  UPDATE "addresses" SET "city" = "New York", "state" = "NY", "zip" = "10108" WHERE user_id = (select id from users where first_name = "Virginie" and last_name = "Mitchell")
*once for 34705 and once for 24028, I feel like a subquery would be better to do this but it seemed like a massive rabbit hole, I might go back and look at this*
- [√] How much would it cost to buy one of each tool?
*46477*
SELECT SUM("price") FROM "items" WHERE "category" LIKE "%Tools%";
- [√] How many total items did we sell?
*2125*
SELECT SUM("quantity") FROM "orders";
- [√] How much was spent on books?
*420566*
SELECT "category", SUM("price" * "quantity") FROM "items" AS I JOIN "orders" AS O ON I.id=O.item_id WHERE "category" = "Books";
- [√] Simulate buying an item by inserting a User for yourself and an Order for that User.
*create user*
INSERT INTO "users" ("first_name", "last_name", "email") VALUES ("Kurtis", "Clauser", "kc@gmail.com")
*create order*
INSERT INTO "orders" ("item_id", "quantity", "user_id", "created_at") VALUES ("25", "42", "51", datetime());
## Adventure Mode

- [ ] What item was ordered most often? Grossed the most money?
*incredible granite car*
SELECT "title", "price", SUM("quantity") FROM "items" AS I JOIN "orders" AS O ON I.id=O.item_id GROUP BY "title" ORDER BY SUM(quantity)desc limit 1;
*incredible granite car*
SELECT "title", SUM("price" * "quantity") FROM "items" AS I JOIN "orders" AS O ON I.id=O.item_id GROUP BY "title" ORDER BY SUM(price * quantity) desc limit 1;

- [ ] What user spent the most?
*user 11 which is Phoebe Kshlerin*
SELECT "user_id", ("price" * "quantity") FROM "items" JOIN "orders" on items.id=orders.id GROUP BY "user_id"ORDER BY (price * quantity) desc limit 1;
- [ ] What were the top 3 highest grossing categories?
