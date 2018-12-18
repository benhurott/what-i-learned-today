# Shell Script

## Basics 

### How to use default arg values

If you want to use a default value if an argument was not setted, you can do:

```bash
# create a variable that receives the default branch
DEFAULT_BRANCH="master"

# here, the "1" is the position of the argument ($1, $2, etc)
FOO=${1:-$DEFAULT_BRANCH}

echo "$FOO"
```

## Git

### Get current git branch name

```bash
echo "$(git branch | grep \* | cut -d ' ' -f2)"
```

### Remove all local branches, but keep one

```bash
function git_remove_local_branches() {
  DEFAULT_BRANCH="master"
  BRANCH_TO_KEEP=${1:-$DEFAULT_BRANCH}
  git checkout master && git branch | grep -v "$BRANCH_TO_KEEP" | xargs git branch -D 
}
```

Sample Usage:

```sh
# will remove all branches, but keep develop
git_remove_local_branches develop
```

## Utilities

### Check who is using a port

```sh
lsof -n -i:$1 | grep LISTEN
```

### Transforming video into a gif using ffmpeg

```sh
ffmpeg -i "$1" -vf scale=500:-1 -t 10 -r 10 "$1.gif"
```

Just replace the `$1` for the path of your video.

### Get iPhone Serial Number

Plug the iphone in your mac and run:

```bash
system_profiler SPUSBDataType -detailLevel mini | grep -e iPhone -e Serial | sed -En 'N;s/iPhone/&/p'
```

## Docker

### Stop all containers

```sh
docker stop $(docker ps -a -q)
```

### Remove all containers

```sh
docker rm $(docker ps -a -q)
```

### Remove all images
```sh
docker rmi $(docker images -q)
```
