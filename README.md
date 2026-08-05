# Batch Connect - OSC Schrodinger

![GitHub Release](https://img.shields.io/github/release/osc/bc_osc_schrodinger.svg)
[![GitHub License](https://img.shields.io/badge/license-MIT-green.svg)](https://opensource.org/licenses/MIT)

## Overview


An [Open OnDemand](https://openondemand.org/) Batch Connect app that launches a [Schrodinger](https://www.schrodinger.com/) Maestro GUI in an XFCE desktop session on the OSC Cardinal cluster. Schrodinger provides a computational platform for drug discovery and materials science.

This app uses the Batch Connect `vnc` template with Slurm.

- **Upstream project:** [Schrodinger](https://www.schrodinger.com/)
- **Batch Connect template:** `vnc`
- **Scheduler:** Slurm

## Screenshots

![Schrodinger running in browser](docs/bc_osc_schrodinger.png)

## Features

- Launches Schrodinger Maestro GUI in an XFCE VNC desktop session
- Hardware-accelerated 3D visualization on `vis` nodes via VirtualGL
  (`vglrun maestro -NOSGL`)
- Software rendering on non-GPU nodes (`maestro -SGL`)
- Multiple Schrodinger versions available
- Configurable cores, wall time, and node type (any, vis, hugemem) via the
  launch form
- License management support for Schrodinger features (macromodel, glide,
  ligprep, qikprep, epik)
- Configurable VNC resolution

## Requirements

### Compute Node Software

This Batch Connect app requires the following software be installed on the
**compute nodes** that the batch job is intended to run on (**NOT** the
OnDemand node):

- [Schrodinger] 2020.1+
- [Lmod] 6.0.1+ or any other `module purge` and `module load <modules>` based CLI used to load appropriate environments within the batch job before launching the Jupyter server.
- [Xfce Desktop] 4+

For VNC server support:

- [TurboVNC] 2.1+
- [websockify] 0.8.0+

For hardware rendering support: 
- [VirtualGL] 2.3+ 

[VirtualGL]: https://virtualgl.org/
[Lmod]: https://www.tacc.utexas.edu/research-development/tacc-projects/lmod
[Xfce Desktop]: https://xfce.org/
[Schrodinger]: https://www.schrodinger.com/
[TurboVNC]: http://www.turbovnc.org/
[websockify]: https://github.com/novnc/websockify

### Open OnDemand
- Tested to work with the latest version of Open OnDemand
- Scheduler: Slurm

## App Installation

### 1. Clone the repository into your Open OnDemand apps directory:

```sh
cd /var/www/ood/apps/sys
git clone https://github.com/OSC/bc_osc_schrodinger.git
cd bc_osc_schrodinger

# Pin to a release (recommended)
git checkout v0.6.0
```

No restart is needed -- Batch Connect apps are not Passenger apps and are
detected automatically.

To update the app you would:

```sh
cd bc_osc_schrodinger
git fetch
git checkout <tag/branch>
```

Again, you do not need to restart the app as it isn't a Passenger app.

### 2. Configure for your site

Edit `form.yml` and update these values for your cluster:

| Attribute | Default | Change to |
|-----------|---------|-----------|
| `cluster` | `cardinal` | Your cluster name(s) |
| `schrodinger_version` | `schrodinger/2024.3` (and others) | Schrodinger versions on your system |
| `node_type` | OSC-specific node types | Node types available on your cluster |
| `num_cores.max`       | `96`                          | Max cores on your compute nodes  |

In `script.sh.erb`, the app loads modules with:
```
module load <schrodinger_version>
```
For GPU/vis nodes, it additionally loads:
```
module load intel/2021.10.0 virtualgl/3.1.1
```
Ensure equivalent modules are available on your system.

### To Update the App

```sh
cd /var/www/ood/apps/sys/bc_osc_schrodinger
git fetch
git checkout <tag>
```

No restart is needed.

## Configuration

### form.yml attributes

| Attribute             | Widget       | Description                                              | Default |
|-----------------------|--------------|----------------------------------------------------------|---------|
| `cluster`             | select       | Target cluster ID(s)                                     | `cardinal` |
| `schrodinger_version` | select       | Schrodinger version / module load string                 | `schrodinger/2024.3` |
| `bc_num_hours`        | number       | Maximum wall time (hours)                                | `1` |
| `bc_num_slots`        | number       | Number of nodes                                          | `1` |
| `num_cores`           | number_field | Number of CPU cores (0--96; 0 = full node)               | `1` |
| `node_type`           | select       | Compute node type (any, vis, hugemem)                    | `any` |
| `licenses`            | text         | Schrodinger license string (e.g., `glide@osc:1`)         | (empty) |
| `bc_vnc_resolution`   | text         | VNC session resolution                                   | (required) |

### Environment variables

| Variable | Required | Description |
|-----------|-------------|---------|
| SCHRODINGER | Yes | Path to Schrodinger installation root |
| LM_LICENSE_FILE | Yes | License server or file for Schrodinger |
| VGL_DISPLAY | No | VirtualGL display for GPU rendering |

## Troubleshooting

### Job starts but app doesn't appear (Batch Connect)

1. Check the job's `output.log` in `~/ondemand/data/sys/bc_osc_schrodinger/`
2. Verify the module loads correctly: `module load schrodinger/<version>`
3. Verify the window manager is installed: `which xfwm4`
4. Verify websockify is installed and accessible

### "Module not found" error

The module name in `form.yml` doesn't match your system. Run `module spider software` to find the correct name and update the `modules` attribute.

### Connection timeout

The app may need more time to start. Increase the connection timeout or check that the compute node can open the required port.

## Testing

| Site | OOD Version | Scheduler | Status |
|------|-------------|-----------|--------|
| Ohio Supercomputer Center | 4.2.2 | Slurm | Production |

## Known Limitations

- Licensing for Schrodinger features must be correctly configured and requested

## Contributing

1. Fork this repository
2. Create a feature branch (`git checkout -b feature/my-improvement`)
3. Submit a pull request with a description of your changes

For bugs or feature requests, [open an issue](https://github.com/OSC/bc_osc_schrodinger/issues).

This app is part of the [OOD Appverse](https://ondemand.connectci.org/affinity-groups/ood-appverse). Join the [Appverse Affinity Group](https://ondemand.connectci.org/affinity-groups/ood-appverse) to connect with other contributors.

## References

- [Schrodinger](https://www.schrodinger.com) — the application launched by this OOD app
- [Open OnDemand](https://openondemand.org/) — the HPC portal framework
- [OOD Batch Connect app development docs](https://osc.github.io/ood-documentation/latest/app-development.html)
- [Changelog](https://github.com/OSC/bc_osc_schrodinger/blob/master/CHANGELOG.md)
  -- release history for this app

## License

* Documentation, website content, and logo is licensed under
  [CC-BY-4.0](https://creativecommons.org/licenses/by/4.0/)
* Code is licensed under MIT (see [LICENSE.txt](/LICENSE.txt))
* The Schrodinger logo is a trademark of [Schrodinger].

## Acknowledgements
This app is built on [Open OnDemand](https://openondemand.org/), developed and maintained by the [Ohio Supercomputer Center (OSC)](https://www.osc.edu/).

Open OnDemand is supported by the National Science Foundation under awards [NSF SI2-SSE-1534949](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1534949) and [NSF CSSI-Frameworks-1835725](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1835725).
