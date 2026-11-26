# Malicious Repositories List

An index of malicious repositories and related resources that impersonate legitimate software (for example UI libraries, game scripts, and developer tools) and use search engine optimization or platform trust to distribute malicious payloads.

## Motivation

Created in response to the proliferation of malicious repositories on GitHub and similar platforms that exploit SEO by masquerading as legitimate resources (e.g., UI libraries).

## Disclaimer

This repository is intended strictly for cybersecurity research, threat intelligence, and educational purposes only. All listed links point to external third-party repositories and are provided "as is" for awareness and defensive analysis. Do not interact with or execute any files from these sources in a non-isolated environment. The maintainer assumes no responsibility or liability for any misuse, damage, or legal consequences resulting from the use of this information.

## Scope

The index was initially focused on GitHub. It now covers malicious resources on multiple platforms, such as GitHub, Roblox, npm, PyPI, and GitLab. The platform of each entry is recorded in the `platform` field.

This repository stores only URLs, metadata, and written analysis. It does not contain malware samples, binaries, or malicious source code.

## Repository Structure

- `data/active_repos.json`: entries that are currently reachable.
- `data/removed_repos.json`: entries that have been taken down or suspended. Retained as a historical record.
- `scripts/add_entry.py`: adds a new entry from a URL.
- `scripts/check_status.py`: checks active entries and moves unavailable ones to the removed file.
- `scripts/validate.py`: validates the data files and schema.
- `.github/workflows/status-check.yml`: runs the status check daily.
- `LICENSE`: CC0 1.0 Universal.

## Defanging

All URLs and domains are defanged to prevent accidental access.

| Original | Defanged |
|---|---|
| `https://` | `hxxps://` |
| `http://` | `hxxp://` |
| `.` in host name | `[.]` |

Example: `hxxps://github[.]com/example-user/fake-ui-library`

## Data Schema

Both JSON files contain an array of objects with the structure below. Unknown scalar values are `null`. Unknown lists are `[]`.

| Field | Type | Description |
|---|---|---|
| `id` | string | Identifier in the form `MR-YYYY-NNNN`. Unique across both files. |
| `platform` | string | `github`, `roblox`, `npm`, `pypi`, `gitlab`, or `other`. |
| `url` | string | Defanged URL of the resource. |
| `author` | string or null | Publisher account name. |
| `resource_type` | string | `repository`, `asset`, `package`, `release`, or `other`. |
| `status` | string | `active` or `removed`. Matches the file containing the entry. |
| `first_seen` | string | Date the entry was first recorded (YYYY-MM-DD). |
| `last_checked` | string | Date of the most recent status check (YYYY-MM-DD). |
| `removed_at` | string or null | Date the resource was found unavailable. Set only in `removed_repos.json`. |
| `disguise.category` | string or null | Type of legitimate software being imitated. |
| `disguise.impersonated_target` | string or null | Name of the legitimate project being imitated. |
| `disguise.seo_keywords` | array | Search terms used to attract victims. Entered manually. |
| `threat_indicators.delivery_method` | string or null | How the payload is delivered. |
| `threat_indicators.malicious_files` | array | Names of malicious files. |
| `threat_indicators.malware_family` | string or null | Malware family, if identified. |
| `threat_indicators.c2_domains` | array | Defanged command and control domains, without protocol or path. |
| `metrics_snapshot.stars` | integer or null | Star count at capture time. GitHub only. |
| `metrics_snapshot.forks` | integer or null | Fork count at capture time. GitHub only. |
| `metrics_snapshot.repo_created_at` | string or null | Creation date of the repository. |
| `metrics_snapshot.captured_at` | string or null | Date the metrics were captured. |
| `notes` | string or null | Short description of the payload or distribution tactics. |

Example entry:

```json
{
  "id": "MR-2026-0001",
  "platform": "github",
  "url": "hxxps://github[.]com/attacker-user/fake-shadcn-ui",
  "author": "attacker-user",
  "resource_type": "repository",
  "status": "active",
  "first_seen": "2026-11-26",
  "last_checked": "2026-11-26",
  "removed_at": null,
  "disguise": {
    "category": "UI Library",
    "impersonated_target": "shadcn/ui",
    "seo_keywords": ["react ui kit", "tailwind components"]
  },
  "threat_indicators": {
    "delivery_method": "Fake Release Binary",
    "malicious_files": ["setup_v2.1.exe"],
    "malware_family": "Generic Stealer",
    "c2_domains": ["api[.]malicious-c2[.]com"]
  },
  "metrics_snapshot": {
    "stars": 1420,
    "forks": 310,
    "repo_created_at": "2026-11-20",
    "captured_at": "2026-11-26"
  },
  "notes": "Cloned official README, injected password-protected archive link in Releases, stars inflated by bot accounts."
}
```

## Usage

Add an entry from a URL:

```bash
python scripts/add_entry.py "https://github.com/owner/repo" --category "UI Library" --target "shadcn/ui" --notes "Short description"
```

Validate the data files:

```bash
python scripts/validate.py
```

Check the status of active entries:

```bash
python scripts/check_status.py
```

Setting the `GITHUB_TOKEN` environment variable raises the GitHub API rate limit.

## Contributing

If you find a malicious resource that impersonates legitimate software:

1. Confirm that the resource is malicious. Examples include obfuscated or encoded payloads, executables in releases, stealer or loader behavior, and hijacked dependencies. Analyze it only in an isolated environment, and record findings as text.
2. Do not attach malware samples, binaries, or source code containing payloads to issues or pull requests.
3. Open a pull request that adds one entry, or open an Issue with the defanged URL and the observed behavior.
4. Run `python scripts/validate.py` before submitting.

## License

This dataset is dedicated to the public domain under the [CC0 1.0 Universal](LICENSE) license.