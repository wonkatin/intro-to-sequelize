# Intro to Sequelize
Sequelize is a library that allows us to interact with our sql database by writing javascript code. In other words, it is going to translate our js commands into sql commands, send them to our sql server, and translate the results into js for us.

We are going to use sequelize to make two tables: one about books, and one about whatever you please!

## Getting started
We will need to install a tool called `sequelize-cli`. This tool will be used by other projects, so we want to install it globally, not just in this project. The `-g` flag in this command tells npm to install on the global level:
```
npm install -g sequelize-cli
```
^^^ You will know this step worked if `which sequelize` gives you a filepath and not "sequelize not found".

Now it's time to install some libraries that only belong in this project, not at the global level. The first step is to create a new npm project in this folder:
```
npm init
```
Check that this worked by looking for the `package.json` file.

Let's install some packages that are only for this project (Note the absence of the `-g` in the next command):
```
npm install sequelize pg
```
You'll know this worked if you now have a `node_modules` directory in your folder, and if your package.json now lists sequelize and pg as dependencies.

Our last step in this section is to make sure we don't commit our node_modules folder. Run `git status` to see all the files that are available for staging, and unfortunately our node_modules folder is in there.

Make a file called `.gitignore`, and write `node_modules` in it. If it worked, you should be able to `git status` again and no longer see node_modules in the red ink. You can now commit as usual.

## Setting up sequelize
From the command line, in this folder, run `sequelize init`. It will create 4 new folders: config, migrations, models, and seeders.

Our first stop is to make some changes in `config/config.json`:
1. You can delete the "test" and "production" objects for this project, since we will never be deploying it. (In our future deployed projects, we will need the production object.)
1. Change the dialect from "mysql" to "postgresql".
1. We need to change the value of "database" from "database_development" to the name of a real db we want to connect to. So take a brief detour to create a new database called `intro_to_sequelize`, and then supply this name for the value of the database key.
1. If you are using a Mac, delete the username and password lines.
1. If you are using Windows or Linux, you will have to fill in your postgres username and password. But you shouldn't commit your password to github! You should open your `.gitignore` file and add `config/config.json` on a new line. Make sure that your `git status` no longer includes config/config.json in the modified files.

## Creating a model
A _model_ is a js class that corresponds to a table in our db. Sequelize gives a handy command for building these gnarly files:
```
sequelize model:generate --name=book --attributes title:string,author:string,publishedYear:integer
```
Run this command, and you'll see that a migration file and a model file got created. Let's look at the anatomy of this command, and the effects it has on those files:

The value after `--name=` will become the name of the model: look in models/book.js, and you'll see `modelName: 'book'`. This is sequelize's official identifier of this model. This value also gets pluralized and inserted into the migration file: `queryInterface.createTable('books'...`.

The key-value pairs that come after `--attributes` have been baked into both the migration file and the model file.

## Running a migration
The migration constitutes a set of instructions that will get sent to our db server, where they'll be translated into a `CREATE TABLE books (...)` command. Just like when we created tables manually in psql, the command only gets run once.

From the command line, we can run
```
sequelize db:migrate
```
and sequelize will run every migration in the migrations folder. Run this command, then use psql to look in your database. Your table is there, complete with all the rows we defined for it!

## Interacting with our db from our js
Our gameplan:
1. Create a js file
1. Put some sequelize commands in it that will add a row to our table
1. `node` it
1. Look at our table in psql and see our shiny new row

### Create a js file
It can be called anything you want and be located anywhere you want. But it's not a bad idea to pick a convention and stick to it throughout your projects. We will use the convention of calling it `dbTest.js`, and putting it in the root of the project.

### Put some sequelize commands in it
```js
const models = require('./models')
models.book.create({
  title: 'Invisible Man',
  author: 'Ralph Ellison',
  publishedYear: 1952
})
```
There is a lot going on here that we will unpack:
- requiring another file
- but... models is a folder, not a file?
- what is models/index.js?
- what is the `models` variable?, and why does it have a `.book` property?
- what is `.create`?

### `node` this file
`node dbTest.js`

### Look at our data in psql
Remember to connect to the "intro_to_sequelize" database first.

## Make your own table
Repeat this entire project in a new folder, with the goal of creating a new table. It should store data on whatever you'd like.
1. Choose what table you want to make. Decide on 3 columns for it (not including id, createdAt, and updatedAt), and choose their data types. Make an ERD for this table. 
1. Make a new folder outside this lab and `cd` into it. You don't have to turn this folder in, it's just for you to practice. But run `git init` just to stay in shape!
1. `npm init` in that folder, and note what gets created.
1. `npm install sequelize`, and `npm install pg`. Check your package.json to confirm the new dependencies, and note the node_modules folder.
1. Now is a good time to make a `.gitignore` and write `node_modules` in it. Make sure that your node_modules don't appear in your git status.
1. `sequelize init` in that folder, and note what gets created.
1. Modify your `config/config.json`. See the *Setting up Sequelize* section above.
1. Using psql or the `createdb` terminal command, create a database with the same name that you put in your config.json.
1. Make a migration & model file using `sequelize model:generate <name, attributes, etc>`
1. Look at the generated files and make sure everything looks good. Pay attention to the columns in the migration, because once this gets run it's a pain to undo!
1. Run the migration with `sequelize db:migrate`
1. Look in psql to confirm that your table got made.
1. Make a dbTest.js, and put a new `create` command in it.
1. `node` your dbTest.js. 
1. Look at your new row in psql! Nice!
