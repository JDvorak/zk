# nuid.zk

Cross-platform zero knowledge proofs.

## ⚠️  This library has not been independently audited.

## Git issues and other communications are warmly welcomed. [dev@nuid.io](mailto:dev@nuid.io)

## Requirements

[`jvm`](https://www.java.com/en/download/), [`node + npm`](https://nodejs.org/en/download/), [`clj`](https://clojure.org/guides/getting_started), [`shadow-cljs`](https://shadow-cljs.github.io/docs/UsersGuide.html#_installation)

## From Clojure and ClojureScript

### tools.deps:

`{nuid/zk {:git/url "https://github.com/nuid/zk" :sha "..."}}`

### usage:

```
$ clj # or shadow-cljs node-repl
=> (require '[nuid.elliptic.curve.point :as point])
=> (require '[nuid.elliptic.curve :as curve])
=> (require '[nuid.cryptography :as crypt])
=> (require '[nuid.bn :as bn])
=> (require '[nuid.zk :as zk])
=> (def protocol {:id "knizk"})
=> (def curve {:id "secp256k1"})
=> (def keyfn {:id "sha256"
               :salt (crypt/salt 32)
               :normalization-form "NFKC"})
=> (def hashfn {:id "sha256"
                :normalization-form "NFKC"})
=> (def secret "high entropy ✅")
=> (def params (zk/coerce {:protocol protocol
                           :curve curve
                           :keyfn keyfn
                           :hashfn hashfn
                           :secret secret}))
=> (def pub (zk/pub params))
=> (def nonce (crypt/secure-random-bn 32))
=> (def params (assoc params :pub pub :nonce nonce))
=> (def good (zk/proof params))
=> (def bad (zk/proof (assoc params :secret "garbage 🚮")))
=> (def params (merge (dissoc params :secret) good))
=> (zk/verified? params)           ;; true
=> (def params (merge params bad)) ;; overwrite good
=> (zk/verified? params)           ;; false
```

## From JavaScript

This library aims to be usable from JavaScript. More work is necessary to establish the most convient consumption patterns, which will likely involve [`transit-js`](https://github.com/cognitect/transit-js).

### node:

```
$ shadow-cljs release node
$ node
> var Crypt = require('<...>/nuid_cryptography')
> var Zk = require('./target/node/nuid_zk');

> var spec = {"protocol": {"id": "knizk"},
              "curve": {"id": "secp256k1"},
              "keyfn": Crypt.scryptParameters(),
              "hashfn": {"id": "sha256", "normalization-form": "NFKC"}};
> var secret = "high entropy ✅"
> var pub = Zk.pub(spec, secret);
> var nonce = Crypt.secureRandomBytes(32);
> var good = Zk.proof(spec, pub, nonce, secret);
> var bad = Zk.proof(spec, pub, nonce, "garbage 🚮")
> Zk.isVerified(spec, pub, nonce, good);
> Zk.isVerified(spec, pub, nonce, bad);
```

### browser:

```
$ shadow-cljs release browser
## go use ./target/browser/nuid_zk.js in a browser script
```

## From Java

To call `nuid.zk` from Java or other JVM languages, use one of the recommended interop strategies ([var/IFn](https://clojure.org/reference/java_interop#_calling_clojure_from_java) or [uberjar/aot](https://push-language.hampshire.edu/t/calling-clojure-code-from-java/865)). Doing so may require modifications or additions to the API for convenience.

## From CLR

[Coming soon](https://github.com/bcgit/bc-csharp)

## Notes

The purpose of `nuid.zk` and sibling `nuid` libraries (e.g. [`nuid.bn`](https://github.com/nuid/bn)) is to abstract over platform-specific differences and provide a common interface to fundamental dependencies. This allows us to express dependent logic (e.g. [`nuid.zk`](https://github.com/nuid/zk)) once in pure Clojure(Script), and use it from each of the host platforms (Java, JavaScript, CLR). This is particularly useful for generating and verifying proofs across service boundaries. Along with [`tools.deps`](https://clojure.org/guides/deps_and_cli), this approach yields the code-sharing, circular-dependency avoidance, and local development benefits of a monorepo, with the modularity and orthogonality of an isolated library.

## Licensing

Apache v2.0 or MIT

## Contributing

Install [`git-hooks`](https://github.com/icefox/git-hooks) and fire away. Make sure not to get bitten by [`externs`](https://clojurescript.org/guides/externs) if modifying `npm` dependencies.

### formatting:

```
$ clojure -A:cljfmt            # check
$ clojure -A:cljfmt:cljfmt/fix # fix
```

### dependencies:

```
## check
$ npm outdated
$ clojure -A:depot

## update
$ npm upgrade -s
$ clojure -A:depot:depot/update
```
