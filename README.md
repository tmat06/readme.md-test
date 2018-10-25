<img src="https://s3.amazonaws.com/devmountain/readme-logo.png" width="250" align="right">

# Project Summary

In this project, we are going to make our first full CRUD back-end that uses a database.

## Step 1

### Summary

In this step, we are going to create a bare-bones server.

### Instructions

- Run `npm init -y`.
- Use npm to install and save `express`, `body-parser`, `dotenv` and `massive`.
- Create a `.env` file.
- Create a `.gitignore` to ignore the `node_modules` folder and the `.env` file.
- Create an `index.js` file.
- Require all the packages that we installed and saved.
- Get your server listening on port `3000`.

<details>

<summary> Detailed Instructions </summary>

<br />

Let's begin by opening a new terminal window and `cd` into the project. Let's create a `package.json` file by running `npm init -y`. Using the `-y` flag, we'll get a package.json file with all the default values. Now that we have a `package.json` file, we can use `npm install --save` to install and save packages to it. Run `npm install --save express body-parser massive dotenv` to get all the packages we'll need for this project.

After that is finished, we can see it created a `node_modules` folder. We never want to include this folder on GitHub, so let's create a `.gitignore` that will ignore `node_modules`. Create a file and name it `.env`. This file also needs to be included in the `.gitignore`. After that, we're ready to start creating our server. Create an `index.js` file and `require` all the packages we install at the top.

```js
const express = require("express");
const bodyParser = require("body-parser");
const massive = require("massive");
require("dotenv").config();
```

Now that our `index.js` file has access to all our packages, let's create a basic server. We'll begin by saving `express()` to a variable called `app`.

```js
const express = require("express");
const bodyParser = require("body-parser");
const massive = require("massive");
require("dotenv").config();

const app = express();
```

Then, we'll want to use our `bodyParser` middleware.

```js
const express = require("express");
const bodyParser = require("body-parser");
const massive = require("massive");
require("dotenv").config();

const app = express();
app.use(bodyParser.json());
```

Finally, we'll want to tell the server to listen on port `3000` and use a `console.log` to tell us when it is listening.

```js
const express = require("express");
const bodyParser = require("body-parser");
const massive = require("massive");
require("dotenv").config();

const app = express();
app.use(bodyParser.json());

const port = process.env.PORT || 3000;
app.listen(port, () => {
  console.log(`Server listening on port ${port}.`);
});
```

</details>

### Solution

<details>

<summary> <code> .gitignore </code> </summary>

```
node_modules
.env
```

</details>

<details>

<summary> <code> index.js </code> </summary>

![Part1Solution](/img/Step1Solution.PNG)

</details>

## Step 2

### Summary

In this step, we are going to add massive to the server so we can connect to a database.

### Instructions

- Open the `.env` file and create a variable called `CONNECTION_STRING` that equals the URI connection string from your Heroku database.
  - Make sure to add `?ssl=true` at end of your connection string.
- Also add a port value to the `.env` with the value being 3000.
- Use `massive` and the `CONNECTION_STRING` to establish a connection.
- In the `.then` callback from `massive`, set `db` on app to equal the database instance.
- Make sure to add a `.catch()` with a callback function.
- In the callback of the `.catch`, `console.log` the error that would be received if the request was rejected.

<details>

<summary> Detailed Instructions </summary>

<br />

Now that we have a basic node server ready to go, let's modify it to connect to a postgres database. Open the `.env` file and create a variable called `CONNECTION_STRING` that equals the URI connection string from your Heroku database, it should look something like this `postgres://username:password@host/dbname?ssl=true`.

Using the `CONNECTION_STRING`, we can invoke `massive` and pass it in as the first argument. This will return a `promise`.

```
CONNECTION_STRING=postgres://username:password@host/dbname?ssl=true
```

```js
massive(process.env.CONNECTION_STRING);
```

We'll want to execute some logic when the promise is fulfilled, so let's chain a `.then` to it. Be sure to capture the database instance in the first parameter.

```js
massive(process.env.CONNECTION_STRING).then(dbInstance => {});
```

Now that we have the `dbInstance`, we can set it onto `app`. Let's have our function return `app.set('db', dbInstance)`.

```js
massive(process.env.CONNECTION_STRING).then(dbInstance => {
  app.set("db", dbInstance);
});
```

Finally, we need to add a `.catch` so that we can `console.log` any error we might receive. Do so by chaining `.catch` after the `.then`. The `.catch` takes a callback function. Name the callback function parameter `err`. If `.catch` is invoked, `err` will be the error that was received. Add a `console.log` to log the error.

```js
massive(process.env.CONNECTION_STRING)
  .then(dbInstance => {
    app.set("db", dbInstance);
  })
  .catch(err => console.log(err));
```

</details>

### Solution

<details>

<summary> <code> index.js </code> </summary>

![Part2Solution](/img/Step2Solution.PNG)

</details>

## Step 3

### Summary

In this step, we are going to create our table and the `.sql` files we'll need to preform operations on our data. The schema for our table will look like:

- product_id - Serial Primary Key
- name - varchar(40)
- description - varchar(80)
- price - integer
- image_url - text

### Instructions

- Create a `product` table in your Heroku database.
- Create a folder called `db`.
  - Create a `create_product.sql` file.
  - Create a `read_products.sql` file.
  - Create a `read_product.sql` file.
  - Create a `update_product.sql` file.
  - Create a `delete_product.sql` file.
- `create_product.sql`:
  - The SQL should be able to add a new product to the `product` table.
  - The SQL should have four parameters ( name, description, price, image_url ).
- `read_products.sql`:
  - The SQL should be able to return all products from the `product` table.
- `read_product.sql`:
  - The SQL should be able to return a specific product from the `product` table.
  - The SQL should use a parameter to find the product whose `product_id` matches.
- `update_product.sql`:
  - The SQL should be able to update the description of a specific product from the `product` table.
  - The SQL should use a parameter to find the product whose `product_id` matches.
  - The SQL should use a parameter to update the value of the `description`.
- `delete_product.sql`:
  - The SQL should be able to delete a specific product from the `product` table.
  - The SQL should use a parameter to find the product whose `product_id` matches.

<details>

<summary> Detailed Instructions </summary>

<br />

Now that we have a method of connecting to our database and have an instance ready to go on `app`, we are ready to start creating the `sql` files that will interact with our database and a `products` table. Let's begin by creating a `products` table that follows the schema in the summary. The final syntax will look like:

```sql
CREATE TABLE product (
  product_id SERIAL PRIMARY KEY NOT NULL,
  name varchar(40) NOT NULL,
  description varchar(80) NOT NULL,
  price integer NOT NULL,
  image_url text NOT NULL
);
```

Now that we have a `product` table, we'll make five `sql` files. One for `creating` products; one for `reading` all products; one for `reading` a specific product; one for `updating` products; and one for `deleting` products. Let's create a folder called `db`. This is where we'll store our `sql` files that we can execute later using the database instance.

Inside the `db` folder, let's create a file called `create_product.sql`. This `sql` file will be responsible for creating a new product using four parameters. The four parameters are `name`, `description`, `price`, and `image_url`. To add something to a database we use the following syntax: `INSERT INTO Table ( column1, column2 ) VALUES ( value1, value2 );` The values we'll change are `Table`, `column`, and `value`. Since we want to insert into the `product` table, we'll change `Table` to `product`. Since we are updating the `name`, `description`, `price`, and `image_url`, we'll use those as the columns. And since we are using parameters for the values, we'll use `$1`, `$2`, `$3`, and `$4` as the values. The final syntax will look like:

```sql
INSERT INTO product ( name, description, price, image_url ) VALUES ( $1, $2, $3, $4 );
```

Now let's move on to `read_products.sql`. Create a file in the `db` folder called `read_products.sql`. This `sql` file will be responsible for reading all products from the database. To read all data from a database we use the following syntax: `SELECT * FROM Table`. Since we are working with the `product` table, we'll change `Table` to `product`. The final syntax will look like:

```sql
SELECT * FROM product;
```

Now let's move on to `read_product.sql`. Create a file in the `db` folder called `read_product.sql`. This file will be very similar to `read_products.sql`, however we need to add a where statement so we don't get all the products. We'll want to use a parameter so we can dynamically select a product by `product_id`. We can use a where statement with the following syntax: `WHERE column1 = value`. Since we are looking for a product by ID, we'll change `column1` to `product_id`. And since we are using a parameter for the ID, we'll change `value` to `$1`. The final syntax will look like:

```sql
SELECT * FROM product WHERE product_id = $1;
```

Now let's move on to `update_product.sql`. Create a file in the `db` folder called `update_product.sql`. This `sql` file will be responsible for updating the description of a product by ID. To update data from a database we use the following syntax: `UPDATE Table SET column1 = value1 WHERE condition`. Since we are working with the `product` table we'll change `Table` to `product`. Since we are updating the description dynamically we'll set `column1` to `description` and `value1` to `$2`. And since we are updating products by ID we'll set `condition` to `product_id = $1`. The order of `$1` and `$2` doesn't matter as long as you following the same order in the `controller` file. I personally prefer `(id, value)` instead of `(value, id)`. The final syntax will look like:

```sql
UPDATE product SET description = $2 WHERE product_id = $1;
```

Now let's move on to `delete_product.sql`. Create a file in the `db` folder called `delete_product.sql`. This `sql` file will be responsible for deleting a specific product by ID. To delete data from a database we use the following syntax: `DELETE FROM Table WHERE condition`. Since we are working with the `product` table, we'll change `Table` to `product`. Since we are deleting by product ID, we'll change `condition` to `product_id = $1`.
The final syntax will look like:

```sql
DELETE FROM product WHERE product_id = $1;
```

</details>

### Solution

<details>

<summary> <code> CREATE TABLE product </code> </summary>

![Part3Solution](/img/Step3Solution.PNG)

</details>

<details>

<summary> <code> SQL </code> </summary>

<details>

<summary> <code> create_product.sql </code> </summary>

![Part3Solution](/img/Step3SolutionCreate.PNG)

</details>

<details>

<summary> <code> read_products.sql </code> </summary>

![Part3Solution](/img/Step3SolutionRead.PNG)

</details>

<details>

<summary> <code> read_product.sql </code> </summary>

![Part3Solution](/img/Step3SolutionRead2.PNG)

</details>

<details>

<summary> <code> update_product.sql </code> </summary>

![Part3Solution](/img/Step3SolutionUpdate.PNG)

</details>

<details>

<summary> <code> delete_product.sql </code> </summary>

```sql
DELETE FROM product WHERE product_id = $1;
```

</details>

</details>
