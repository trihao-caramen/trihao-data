name: Publish TriHao Release
on:
  push:
    tags:
      - 'v*.*.*'

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Create manifest
        run: |
          RAW_URL="https://raw.githubusercontent.com/${{ github.repository }}/main/TriHaoTest_v2.py"
          sha=$(sha256sum TriHaoTest_v2.py | cut -d' ' -f1)
          jq -n --arg v "${GITHUB_REF##*/}" --arg url "$RAW_URL" --arg checksum "$sha" \
            '{version:$v, url:$url, checksum:$checksum}' > trihao_manifest.json
      - name: Upload manifest to release
        uses: softprops/action-gh-release@v1
        with:
          files: trihao_manifest.json
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
