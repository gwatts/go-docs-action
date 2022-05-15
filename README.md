# Go Docs Action

This action uses [godoc-static](https://code.rocketnine.space/tslocum/godoc-static) to generate a static HTML copy of a repos Go source documentation.

It will place the HTML output into a temporary directory and return the directory name in a step output, which can be used by subsequent workflow steps to upload the documentation and make it accessible.

## Example Workflow

```yaml
name: Go Docs
on: 
  push:
  pull_request:

jobs:
  godocs:
    - uses: actions/checkout@v3
    - uses: actions/setup-go@v3
    - uses: actions/cache@v2
      with:
        path: |
          ~/.cache/go-build
          ~/go/pkg/mod
        key: ${{ runner.os }}-go-${{ hashFiles('**/go.sum') }}
        restore-keys: |
          ${{ runner.os }}-go-

    - name: generate godocs
      id: godocs
      uses: gwatts/go-docs-action@v1
      with:
        title: 'My Project'
        description: 'Docs for branch ${{ github.ref.name }}'
    
    - name: upload docs to s3
      run: aws s3 sync --delete --only-show-errors ${{ steps.godocs.outputs.output-path }}/ s3://my-bucket/prefix/
```
