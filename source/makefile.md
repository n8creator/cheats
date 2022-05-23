# Makefile

## 1. Cheat sheet

### 1.1. Makefile basics

This is how basic Make shortcut looks like:

```makefile
test:  # command name (`kebab-case` or `snake_case` supported)
    MINITEST_REPORTER=SpecReporter bundle exec bin/rails test
```

The original purpose of Make is to automate complex builds for C/C++ projects. So, the semantics
of `make test` assumes that `test` directory should be generated as a result.

To persuade Make add one magical rule to Makefile (if there are multiple commands like this, we can add them all):

```Makefile
.PHONY: app test log doc
```

Another surprise is that Make is very picky about indentation. It _refuses to work if you use spaces_:

```sh
$: make test
Makefile:13: *** missing separator. Stop.
```

### 1.2. Running multiple commands at once

You can combine commands in long chains. *If one fails, the rest of them going to be skipped*:

```sh
make test deploy
```

### 1.3. Subcommands

Next command means that each time you run `make deploy`, `make test` is called first. _And only if it succeeds, `deploy` will be executed_:

```makefile
deploy: test
    ansible-playbook -i inventory/production --tags ‘deploy’ # ...
```

### 1.4. Aliases

Have trouble remembering Make shortcuts or team members use different shortcuts? Use `aliases` to cope problem:

```makefile
db-migrate:
    bundle exec bin/rails db:migrate

db_migrate: db-migrate
dbmigrate: db-migrate

.PHONY db-migrate db_migrate dbmigrate
```

### 1.5. Miltiline commands

We can run multiple commands under one shortcut, and you can nest make shortcuts:

```makefile
db:
    bundle exec bin/rails db:migrate
    make schema-reset
schema-reset:
    git checkout HEAD -- db/schema.rb
```

Make prints each command before executing it, so the _output is a bit verbose_:

```sh
$: make db
bundle exec bin/rails db:migrate
# ...
make schema-reset
git checkout HEAD -- db/schema.rb
```

### 1.6. Making Make less verbose

When you don't want a command to be printed, and just want Make to execute it, prepend it with `@`:

```makefile
db:
    bundle exec bin/rails db:migrate
    @make schema-rese
```

### 1.7. Ignoring errors

Imagine your team decided to run tests before deploying to staging as well:

```makefile
staging-deploy:
    @make test
    ansible-playbook -i inventory/staging --tags ‘deploy’ #...
```

However, _sometimes you have to deploy to staging even if tests are failing_.

To keep tests running, but still deploy even if they do not pass, we add another magical prefix: `-`.

```makefile
staging-deploy:
    -@make test
    ansible-playbook -i inventory/staging --tags ‘deploy’ #...
```

### 1.8. Running command only if another one fails

There is one more way to achieve the same effect by using regular shell scripting:

```makefile
staging-deploy:
    @make test || true
    ansible-playbook -i inventory/staging --tags ‘deploy’ #...
```

### 1.9. Passing arguments

Imagine you need to pass an agrument to Make shortcut. This can be done by using `$(ARG)` variable, like:

```makefile
taging-fetch:
    scp app@staging-server.dev:/path/to/app/$(ARG)/ ./
```

Then you may call Make shortcut like this:

```sh
make staging-fetch ARG=db/dump.tgz
```

### 1.10. Default arguments

We can use default arguments in Makefile:

```makefile
HELLO?=World # Question mark indicates that the variable is optional. The value after the assignment can be omitted.

say:
    echo "Hello, $(HELLO)!"
```

Let's call the shortcut with the default argument:

```sh
$ make say
echo "Hello, World!"
Hello, World!

$ make say HELLO=Kitty
echo "Hello, Kitty!"
Hello, Kitty!
```

## 2. Problems met and their solutions

### 2.1. A useless message about entering and exiting the working directory is printed in output

This is how the message looks like:

```sh
make[1]: Entering directory '/home/n8creator/some/path'
some output here
make[1]: Leaving directory '/home/n8creator/some/path'
```

**Solution**

Simply add this line to the Makefile:

```makefile
MAKEFLAGS += --no-print-directory
```

## 3. Useful links

- [Modern Make Handbook for modern developers](https://makefile.site/)
- [What is a Makefile and how to start using it (in Russian)](https://guides.hexlet.io/ru/makefile-as-task-runner/)