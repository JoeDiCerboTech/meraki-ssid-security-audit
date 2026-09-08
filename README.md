# Meraki SSID Security Audit

Audit wireless SSID security settings across one or all Cisco Meraki organizations your Dashboard administrator account can access, then export the results to CSV.

The script uses the Cisco Meraki Dashboard REST API directly with Python's standard library. **No Meraki Python SDK and no third-party Python packages are required.**

## What it checks

For every wireless network and SSID it can access, the report captures:

- Organization and network
- SSID name and number
- Enabled/disabled state
- Authentication mode
- Encryption mode
- WPA mode
- VLAN tagging / default VLAN
- PSK vs. Enterprise classification
- Splash page
- SSID visibility
- Band selection
- Minimum bitrate
- AP availability
- Risk level
- Security notes
- Meraki Dashboard URL

## Risk levels

The built-in scoring is intended as a quick audit helper:

| Risk | Examples flagged by the script |
| --- | --- |
| **High** | Open authentication, open encryption, WEP |
| **Medium** | Pre-shared key SSIDs, WPA1/WPA2 mixed mode, Enhanced Open |
| **Low** | 802.1X Enterprise or iPSK when no obvious weak setting is detected |
| **Disabled** | SSID is disabled |
| **Informational** | No obvious weak setting was flagged |

> Risk scoring is not a replacement for reviewing firewall rules, client isolation, guest segmentation, RADIUS configuration, splash settings, or the intended business use of each SSID.

## Requirements

- Python 3
- A Cisco Meraki Dashboard API key
- A Dashboard administrator account with access to the organization(s) you want to audit
- Internet access to `api.meraki.com`

No `pip install` step is required.

## Run it

Open PowerShell in the folder containing the script:

```powershell
py .\Meraki-SSID-Security-Report-REST.py
```

If `py` is not recognized:

```powershell
python .\Meraki-SSID-Security-Report-REST.py
```

The API key prompt is hidden while you paste/type the key.

You can also provide the key through an environment variable for automation:

```powershell
$env:MERAKI_DASHBOARD_API_KEY = "YOUR_API_KEY"
py .\Meraki-SSID-Security-Report-REST.py
Remove-Item Env:\MERAKI_DASHBOARD_API_KEY
```

## Select organizations

After authentication, the script lists the Meraki organizations your account can access.

At `Org selection:` you can:

- Press **Enter** or type `ALL` to scan every accessible organization
- Enter the number shown next to one organization
- Enter part of an organization name
- Enter an organization ID

If multiple organizations match a partial name, the script lets you choose one match or all matches.

## Output

The CSV is written to the current working directory using a name similar to:

```text
Meraki_SSID_Security_Report_all_orgs_20260907_225900.csv
```

A sanitized example is included as [`sample_output.csv`](sample_output.csv).

## API key security

Treat a Meraki API key exactly like a password.

- Do not commit an API key to GitHub.
- Do not put it in screenshots, videos, tickets, email, Teams, or chat.
- Do not save it inside this script.
- If a key is exposed, revoke it immediately and generate a new one.
- The included `.gitignore` excludes common local secret files and generated reports.

## Troubleshooting

### 401 Unauthorized

Check that the key was pasted correctly, has not been revoked, and belongs to a Meraki Dashboard administrator with API access.

### 403 Forbidden for an organization

The organization may be expired/unlicensed, or the administrator associated with the key may not have sufficient rights. The script continues where possible and prints skipped/error entries at the end.

### No wireless networks found

The selected organization may not contain MR wireless networks, or the API key may not have access to those networks.

### `py` or `python` is not recognized

Install Python 3, close and reopen PowerShell, and try again.

## Notes

- The script handles Meraki API pagination.
- HTTP 429 rate limits are retried using the API's `Retry-After` value when available.
- Generated CSV data can contain organization names, network names, SSID names, network IDs, and other customer information. Review the file before sharing it publicly.

## Disclaimer

Use this script only with Meraki organizations you are authorized to administer. Validate findings before making production security changes.

## License

MIT License. See [`LICENSE`](LICENSE).
