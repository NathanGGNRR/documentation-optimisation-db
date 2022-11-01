# GitFlow - BDDIIAGE

![image.png](/.attachments/image-3fe1095d-9094-40ce-9eea-ffb893df4b1a.png)

Here is an explaination about how our GitFlow works.

## Master

The master branch always contains latest up-to-date and tested version of our code. It's the branch used for deployment, the production version is always in it.

## Pre-Prod

The pre-production branch is used to make last test before going to production. It can be used as a branch to base our pre-production deployment on it. It's also used to make urgent "hot-fixes", if the production needs it.

## Develop

This branch is used to merge every new feature, and to make some cleanup (check if everything works like it should, removing useless code, comment everything if it wasn't done before, ...).

## Feature branches

Theses branches are used for feature development. Imagine you want to implement a new feature in the code. Create a new branch with the name "Feature/FeatureName", and do all of your work on it.

# Merge code between branches

The only way to merge code between branches is to make a pull request. If some conflicts are present, please fix them before validating the pull request.

If you ever need to come back to a working version on one of your branches, always use master as a base line, since it's the latest working version available at any point of time.