# Read me
Example chart for how one could possibly implement a canary deployment with [helm](https://helm.sh) and [ambassador](https://getambassador.io)

This chart alternates between two deployments and services to enable going from one version to the next by moving traffic over in a canary fashion.


| Parameter                       | Description                                | Default                                                    |
| ------------------------------- | ------------------------------------------ | ---------------------------------------------------------- |
| `mainTrack` | Current main track | `A`
| `trackA.tag` | Image tag for track A | `v1.0.0`
| `trackB.tag` | Image tag for track B | `v2.0.0`
| `canaryEnabled` | Canary track enabled | `false`
| `canaryWeight` | Ambassador weight for canary (1-100) | `25`
| `replicaCpunt` | Total replicas between tracks, balanced between tracks by weight | `8`



## Initial deploy
By default canary deploy is disabled and track A is default.
```sh
helm template . \
  --set trackA.tag=v1.0.0
```

## Enable canary
Start canary rollout with 25% traffic to deployment B as the canary.
```sh
helm template . \
  --set canaryEnabled=true \
  --set canaryWeight=25 \
  --set trackB.tag=v2.0.0 \
```

## Scale canary traffic and replicas deploy
Increase canary weight to give it more traffic, here 50%
```sh
helm template . \
  --set canaryWeight=50
```

## Promoting the canary
Switch the main track and disable canary
```sh
helm template . \
  --set canaryEnabled=false \
  --set mainTrack=B
```

## Starting over with new canary
Next time around A will be your canary track
```sh
helm template . \
  --set canaryEnabled=true \
  --set canaryWeight=25 \
  --set trackA.tag=v3.0.0 \
```
