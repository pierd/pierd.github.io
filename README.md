[![License](https://i.creativecommons.org/l/by/4.0/88x31.png)](https://creativecommons.org/licenses/by/4.0)

# pierd.github.io site

## Generating locally

Build jekyll docker image:
```
docker build -t jekyll .
```

Run jekyll container with the page mounted and port exposed:
```
docker run --mount type=bind,source="$(pwd)",destination=/home/jekyll,consistency=cached -p 4000:4000 jekyll
```
