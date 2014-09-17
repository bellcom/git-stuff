Bellcom Git
===========

Purpose of this document is to describe how sites are handleded in Git.


Structure
---------

In this section shop.sik.dk is used as an example.

```
  shop.sik.dk
  |-- public_html
  |-- logs
  |-- sessions
  |-- tmp
  |-- site
  |-- .gitignore
  `-- ...

```

### Why ?

The reason we have include a level above public_html is that it allows us to add documentation to the project repository, without having it acessible from outsiders on the webserver.

### .gitignore

This is the contents of a basic .gitignore in the top level.

```
logs
site
sessions
tmp
```

### !Important

* Ignore all files that are related to content. If we stick to the Drupal example, this would be the `files` folder in `sites/default` or equivalent.
* Ignore all files containing sensitive information. ex. `settings.php`
  (Drupal contains a .gitignore, that does this, this would we located in public_html.)


Development
-----------

All new features, are to be made in seperate (feature) branches on the repository.

The production sites using the repo, cannot (must not) work off a feature branch. All releases must be tagged, see "Version naming". And only releases (tags) must be checked out on production.

Test (development) sites can use feature branches. The feature branch beeing tested must be up to date with master. So that it represents what will eventually be put in production.

### Branch naming
- freature branches must be prepended "feature/" followed by a short description of the feature, including a story/ticket number if one exists.
- hotfix branches are prepended "hotfix/" followed by a description, like feature branches.

### Example

In the example below. A project needs 2 new features.

```
          1.      2.      3. 4. 5.

      7.x-1.3                7.x.1.4
  --*-----*--------------------* master
          |\                  /
          | *------*-----*---* feature/#1-Awesome_feature
           \            /
            *----*--*--* feature/#2-Even_awesomer_feature

```



##### 1.
- Version 7.x-1.3 is currently in production
- Two feature branches are based of master at this point, #1-Awesome_feature and #2-Even_awesomer_feature.

##### 2.
- Development happens in the two branches.

##### 3.
- The two features are ready for test. To be able to test the 2 at the same time, feature/#2-Even_awesomer_feature is merged into
- feature/#1-Awesome_feature, and this branch is checked out on the test server.

##### 4.
- Something needed fixing, so this was done in the feature/#1-Awesome_feature branch.

##### 5.
- Everything is great and the feature is merged into master and the master branch is tagged with the next release number. This tag can now be checked out on production.

Test
----

This is targeted releases of larger products that undergo both alpha, beta and qualification. But the principles can be used for projects that only have one testperiod as well.

### Error/bugfixing

Error and bugfixing will begin ASAP after the first issue is reported. All changes will be made in a branch named after the issue(s) beeing adressed.

End of workday:
All fixes ready for deployment must be pushed to master, and a pull-request created.

Start of (next) workday:
All submitted pull-requests will be reviewed and merged by the assignee. After all merges have been approved and merged, the master branch will be tagged with the next release version.



Version naming
--------------

### Drupal projects

Must adhere to Drupals release naming conventions, see: https://www.drupal.org/node/1015226

Example for a Drupal 7, first release
Alpha test period:
  First alpha release
  7.x-1.0-alpha1

Beta test period:
  First beta release
  7.x-1.0-beta1

Qualification period:
  First qualification (rc) release
  7.x-1.0-rc1


### Prestashop projects

Input needed.

### eZ publish projects

Input needed.


Migrating existing site
-----------------------

One way to handle migrating an existing site to a single git repository could be the following. Again we use a Drupal site as our example, in this case with just a "default" site configured.

### Create a copy of the files in the site

```
$ cd /var/www/{{SITENAME.TLD}}

# -r = recursive; -L = copy links to files, exclude content files (if there is not enough space free on the server to copy the files as well)
$ rsync -rL --exclude="public_html/sites/default/" public_html/ public_html_git/

$ cd public_html_git

# remove all .git folders
$ find . -type d -name .git -exec rm -rf {} \;

# move swap old folder with the new one
$ cd ..
$ mv public_html public_html_before_git
$ mv public_html_git public_html

# create .gitignore (remember to ignore public_html_before_git)
$ vim .gitignore

# init, add, commit, push
$ git init
$ git add .
$ git commit -am 'Initial commit'
$ git remote add origin <git repo path>
$ git push -u origin master
```

If all is well, remove the "public_html_before_git" folder, so that it doesn't take up space.
