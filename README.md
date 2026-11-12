# oci-a1-retry

GitHub Actions workflow that retries creating an Oracle Cloud **Always Free**
`VM.Standard.A1.Flex` instance (2 OCPU / 12 GB, eu-frankfurt-1) every 15
minutes until there's free capacity, cycling through every availability
domain you list. Stops retrying once an instance named `a1-video-worker`
is running.

## Required repository secrets

(Settings → Secrets and variables → Actions → **Secrets**)

| Name | Where to find it |
|---|---|
| `OCI_USER_OCID` | Console → Profile icon → User settings → copy the `ocid1.user...` value |
| `OCI_TENANCY_OCID` | Console → Profile icon → Tenancy → copy the `ocid1.tenancy...` value |
| `OCI_FINGERPRINT` | Console → Profile icon → User settings → API keys → the fingerprint of your API signing key |
| `OCI_PRIVATE_KEY` | Full contents of the `.pem` private key downloaded when you created the API signing key |
| `OCI_SSH_PUBLIC_KEY` | Contents of an SSH **public** key (e.g. `~/.ssh/oci_a1.pub`) you'll use to log in to the instance |

## Required repository variables

(Settings → Secrets and variables → Actions → **Variables**)

| Name | Example | Notes |
|---|---|---|
| `OCI_REGION` | `eu-frankfurt-1` | your home region |
| `OCI_COMPARTMENT_OCID` | `ocid1.tenancy.oc1..aaaa...` | root compartment = tenancy OCID |
| `OCI_SUBNET_OCID` | `ocid1.subnet.oc1.eu-frankfurt-1.aaaa...` | create a VCN + public subnet once by hand (not capacity-limited, always works) |
| `OCI_ADS` | `eu-frankfurt-1-AD-1,eu-frankfurt-1-AD-2,eu-frankfurt-1-AD-3` | comma-separated, no spaces |

The image OCID is **not** a variable — the workflow looks up the latest
Ubuntu 24.04 (aarch64) image automatically on every run, so it never goes
stale.

## Running it

- Runs automatically every 15 minutes (`schedule` trigger).
- Trigger a run immediately from the **Actions** tab → *OCI A1 Retry* →
  **Run workflow**.
- Once it succeeds, check Compute → Instances in the OCI console for the
  public IP, then:

  ```bash
  ssh -i ~/.ssh/oci_a1 ubuntu@<public-ip>
  ```
