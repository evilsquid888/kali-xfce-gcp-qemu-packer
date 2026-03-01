# Kali on GCP, QEMU/libvirt builder

Packer templates for building Kali Linux images on Google Compute Platform with the GCP guest environment pre-configured.

## Quick start

Fill in required fields (`project_id`, `bucket`) in `templates/kali-2025-4.json`. Then:

	packer build templates/kali-2025-4.json
	packer build templates/kali-nested-virt.json

### Debugging

	PACKER_LOG=1 packer build -on-error=ask templates/kali-2025-4.json

## Templates

| Template | Kali Version | Notes |
|---|---|---|
| `kali-2025-4.json` | 2025.4 | Current. Bookworm-based, modern GCP provisioning |
| `kali-2020-2.json` | 2020.2 | Legacy. Buster-based |
| `kali-2019-3.json` | 2019.3 | Legacy. Stretch-based |
| `kali-nested-virt.json` | — | Adds nested virt license to an existing GCP image |

## Configuration

Things you need to customize in the template file:

* Configure the `googlecompute-import` post-processor:
	* Add your GCP bucket name (you'll need to create the bucket first)
	* Add your GCP project ID
	* Add the filename to a GCP service account private key file that has access to create images, access GCP buckets, etc.
* Specify a public SSH key — this will be put into `/root/.ssh/authorized_keys`. SSH login with key is permitted for root.

Then:

	packer build templates/kali-2025-4.json

## A pentesting lab within Kali on GCP

I have some vagrant boxes that have configs that put them on a network so that they
can be reached by kali, the host. Uses `libvirt` / `virt-manager`. Requires a little
bit more configuration of kali, build script is in the vagrantfile in the [kali-pentest-lab](kali-pentest-lab) folder.

And my vagrant boxes are [here](https://app.vagrantup.com/deargle).

## Todos

* Add the nested-virtualization license to the build step
* Set up virt-manager with security class VMs (see [https://daveeargle.com/security-assignments/](https://daveeargle.com/security-assignments/))

## Changelog

### 2025.4 (2026-02-28)

* Add Kali 2025.4 template and preseed (`kali-2025-4.json`, `preseed-2025-4.cfg`)
* Update GCP apt repo from `buster` to `bookworm` (matching Kali's current Debian base)
* Switch from deprecated `apt-key add` to modern `signed-by` GPG key management
* Remove `libjson-c3` manual install workaround (no longer needed on bookworm)
* Remove OpenSSH downgrade workaround (modern GCP SSH-in-browser works with current OpenSSH)
* Regenerate SSH host keys via `dpkg-reconfigure openssh-server`
* Add `non-free-firmware` to preseed for bookworm compatibility
