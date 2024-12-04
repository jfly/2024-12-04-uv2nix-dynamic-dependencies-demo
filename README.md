Demonstration of an `uv` workspace that contains a subpackage (`dynamic-deps`)
which has a dynamic dependency on `tqdm`.

`uv` handles this well, note how `tqdm` ends up in the lockfile:

```shell
$ nix develop .#impure --command uv lock
Resolved 4 packages in 1ms
$ grep -B1 -A 5 '^name = "dynamic-deps"' uv.lock
[[package]]
name = "dynamic-deps"
version = "0.1.0"
source = { directory = "dynamic-deps" }
dependencies = [
    { name = "tqdm" },
]
```

However, `uv2nix` misses the dependency on `tqdm`:

```shell
$ nix develop .#uv2nix --command python -c 'import dynamic_deps'
Traceback (most recent call last):
  File "<string>", line 1, in <module>
  File "/home/jeremy/tmp/uv2nix-dynamic-dependencies-demo/dynamic-deps/src/dynamic_deps/__init__.py", line 1, in <module>
    import tqdm
ModuleNotFoundError: No module named 'tqdm'
```
