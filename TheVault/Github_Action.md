GitHub Actions can be used for a number of automation cases and can also be used CI-CD pipelines. It basically runs on a `VM` of GitHub but you can also attach your own machine and then the actions will run on your machines.

Here is a basic basic GitHub Workflow
- it publishes image to the `GHCR` whenever there is a pull-request merge or a push in the main branch with tag of the form `v*.*.*`
- Tags the image with latest if no tags are there, if git tags are present then it releases the image with same git tags. Tags will be transformed like the following example  `(git-tag) v1.2.3 -> 1.2.3 (image-tag)`

```yaml
name: Name or Description of this Workflow
on:
  push:
    branches:
      - main
    tags:
      - 'v*.*.*'
  pull-request:
    types:
      - closed

env:
  REGISTRY: ghcr.io

jobs:
  build-and-push: # name of the job
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.pull_request.merged == true)
    runs-on: ubuntu-latesst # use this label for running on ubuntu
    permissions:
      contents: read
      packages: write
    
    steps:
      - name: Convert repository name to lowercase
        id: repo
        run: echo "lowercase=${GITHUB_REPOSITORY,,}" >> $GITHUB_OUTPUT
	    # above syntax in bash converts the value GITHUB_REPOSITORY
	    # env variable to lowercase, then writting the whole string
	    # to the file specified by $GITHUB_OUTPUT env variable
	    
	  - name: checkout repository
	    uses: actions/checkout@v5 
	    # action defined by actions/checkout repo on github, it 
	    # checksout the repository

      - name: setup docker buildx for caching and optimization
        uses: docker/setup-buildx-action@v3
        # action defined by docker/setup-buildx-action repo on github
        
      - name: login to GHCR
        uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }} # using env var defined above
          username: ${{ github.repository_owner }} # org or user
          password: ${{ secrets.GITHUB_TOKEN }} # TOKEN specified in each run

	  - name: extract metadata for tagging
	    id: meta
        uses: docker/metadata-action@v5
        with:
          images: ${{ env.REGISTRY }}/${{ steps.repo.outputs.lowercase}}
          # here we access the lowercase repo name we saved in 1st step
          tags: |
            type:raw,value=latest
            type:semver,patern:{{version}}

	  - name: Build and push Docker images
	    uses: docker/build-push-action@v5
	    with:
	      context: .
	      push: true
	      tags: ${{ steps.meta.outputs.tags }}
		  labesl: ${{ steps.meta.outputs.labels }}
		  cache-from: type=registry,ref=${{ env.REGISTRY }}/${{ steps.repo.outputs.lowercase }}:buildcache
		  cache-to: type=registry,ref=${{ env.REGISTRY }}/${{ steps.repo.outputs.lowercase}}:buildcache,mode=max

	  - name: Output image info
	    run: |
	      echo "### Docker Image Published!" >> $GITHUB_STEP_SUMMARY	  
	      echo "" >> $GITHUB_STEP_SUMMARY	  
	      echo "**Image:** \`${{ env.REGISTRY }}/${{ steps.repo.outputs.lowercase }}\`" >> $GITHUB_STEP_SUMMARY	  
	      echo "" >> $GITHUB_STEP_SUMMARY	  
	      echo "**Tags**" >> $GITHUB_STEP_SUMMARY	  
	      echo "\`\`\`" >> $GITHUB_STEP_SUMMARY	  
	      echo "\`\`\`" >> $GITHUB_STEP_SUMMARY	  
	      echo "${{ steps.meta.outputs.tags }}" >> $GITHUB_STEP_SUMMARY	  
	      echo "\`\`\`" >> $GITHUB_STEP_SUMMARY
	      # This is just for a pretty output info about image and tags
```
- You might want to go through these resources to understand the syntax much better
	- [checkout action](https://github.com/actions/checkout)
	- [buildx action](https://github.com/docker/setup-buildx-action)
	- [metadata-action](https://github.com/docker/metadata-action)
	- [login-action](https://github.com/docker/login-action)
	- [build-push-action](https://github.com/docker/build-push-action)
	- [caching](https://docs.docker.com/build/ci/github-actions/cache/#registry-cache)
