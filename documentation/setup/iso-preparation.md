# Guest installation media

**Phase 02B verification (28 November 2026).** The Ubuntu Server ISO was downloaded from Canonical. The detached manifest signature produced a valid signature for the published fingerprint below. The full local ISO measured 4,080,486,400 bytes and matched the exact SHA-256 entry for this filename. Windows acquisition is deferred to its endpoint phase. Guest boot remains Not Tested.

## Choose two images

| Guest | Media and publisher filename | Expected length | Use |
| --- | --- | ---: | --- |
| SOC-SRV and LIN-ENDPOINT | Ubuntu Server 24.04.5 LTS, amd64 live server, `ubuntu-24.04.5-live-server-amd64.iso` | 4,080,486,400 bytes (3.80 GiB) | One verified ISO serves both guests. Use under Ubuntu's distribution terms. [Release directory](https://releases.ubuntu.com/24.04/) · [Ubuntu policy](https://ubuntu.com/legal/intellectual-property-policy) |
| WIN-ENDPOINT | Windows 11 Enterprise Evaluation 26H2, x64, English (United States), `26300.9457.260913-1737.26h2_ge_release_svc_refresh_CLIENTENTERPRISEEVAL_OEMRET_x64FRE_en-us.iso` | 8,225,329,152 bytes (7.66 GiB) | A 90-day option for eligible evaluators. [Evaluation terms and audience](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-11-enterprise) · [Microsoft downloads](https://www.microsoft.com/en-us/evalcenter/download-windows-11-enterprise) |

The two images together occupy about 11.46 GiB of the planned 12 GiB ISO allowance. Use one Windows image. Microsoft also offers Windows 11 Pro through the 26H2 x64 multi-edition retail ISO. Choose that route if you have a license that covers the guest, including any applicable school entitlement. Check the rights for the VM separately from the host activation. That file has a different name, size and digest. The download page provides the current language-specific hash under **Verify your download**. [Microsoft Windows 11 download](https://www.microsoft.com/en-us/software-download/windows11)

Microsoft describes the Enterprise trial as an evaluation for IT professionals evaluating on behalf of their organization. Confirm that its terms fit your use before selecting it. Register directly with Microsoft, activate the evaluation, record its expiry privately and plan the lab timeline within 90 days. Microsoft states that an expired or unactivated evaluation displays a warning and shuts down hourly. [Microsoft Evaluation Center](https://www.microsoft.com/en-us/evalcenter/download-windows-11-enterprise)

## Acquire the files after approval

1. Use the existing workspace-level `iso-images` directory on E, a sibling of the Git checkout. Save Ubuntu's two verification files next to its ISO. Run the PowerShell examples from the **repository root**.
2. From the [Canonical 24.04 release directory](https://releases.ubuntu.com/24.04/), choose the exact server filename above, [SHA256SUMS](https://releases.ubuntu.com/24.04/SHA256SUMS) and [SHA256SUMS.gpg](https://releases.ubuntu.com/24.04/SHA256SUMS.gpg).
3. For the Enterprise trial, open the [Microsoft selection page](https://www.microsoft.com/en-us/evalcenter/download-windows-11-enterprise), choose English (United States) and the 64-bit **Enterprise** column. LTSC, Arm64 and other languages use different checksums.
4. If the vendor refreshes a file, record the replacement version, official link, filename, byte count and corresponding publisher hash before comparing it. Keep vendor registration details and VM license material private.

## Authenticate the Ubuntu checksum list

On a system with an existing GnuPG installation, use Canonical's published full signing-key fingerprint, check for a good signature and verify the checksum file. The following example uses PowerShell and an existing `gpg` command. The same GnuPG commands work in an existing Ubuntu environment when run from the media directory. Owner approval is required before adding missing software. [Canonical verification guide](https://ubuntu.com/tutorials/how-to-verify-ubuntu)

```powershell
$mediaDir = (Resolve-Path -LiteralPath '..\iso-images').Path
$keyFingerprint = '843938DF228D22F7B3742BC0D94AA3F0EFE21092'
gpg --keyserver hkps://keyserver.ubuntu.com --recv-keys $keyFingerprint
if ($LASTEXITCODE -ne 0) { throw 'Could not retrieve Ubuntu signing key.' }
gpg --fingerprint $keyFingerprint
gpg --status-fd 1 --verify (Join-Path $mediaDir 'SHA256SUMS.gpg') (Join-Path $mediaDir 'SHA256SUMS')
if ($LASTEXITCODE -ne 0) { throw 'Checksum list signature check failed.' }
```

Match the primary fingerprint in the successful `VALIDSIG` output to the full fingerprint independently published by Canonical. Look up the **exact server filename** in that signed list. On 27 November 2026 its published SHA-256 was `97f3d7ffb032c3eb3b23d2c8be9cc76e60c2c1f2c0146ba5ba9fe01cafae0fd8`. Leave the signature check pending if you cannot authenticate the signing key. [Canonical checksum list](https://releases.ubuntu.com/24.04/SHA256SUMS)

## Compare each local ISO with the publisher

Use the signed Ubuntu manifest for the server image and Microsoft's **Enterprise Eval x64 EN-US** row for the proposed Windows evaluation image. Microsoft published `BC3F24086EBADC94489066B5AD78089E2CF5C3491E90E790BB81A2B199C10E38` for that Windows image. [Microsoft 26H2 Enterprise evaluation hashes, KB5130867](https://support.microsoft.com/en-us/servicing/os/windows/docs/2026/09/verify-the-authenticity-of-a-windows-11-enterprise-evaluation-iso-file)

Run for each acquired image independently. The expected SHA-256 must come from the selected publisher record. Copying the locally calculated value into the prompt would defeat this check.

```powershell
$mediaDir = (Resolve-Path -LiteralPath '..\iso-images').Path
$fileName = (Read-Host 'Exact ISO filename in iso-images').Trim()
if ($fileName -ne [IO.Path]::GetFileName($fileName) -or $fileName -notlike '*.iso') {
    throw 'Enter a single ISO filename.'
}
$image = Get-Item -LiteralPath (Join-Path $mediaDir $fileName) -ErrorAction Stop
$expected = (Read-Host '64-character SHA-256 from the publisher').Trim()
if ($expected -notmatch '^[A-Fa-f0-9]{64}$') { throw 'Invalid SHA-256 format.' }
$actual = (Get-FileHash -LiteralPath $image.FullName -Algorithm SHA256).Hash
if ($actual -ine $expected) { throw 'Digest mismatch. Do not attach this ISO.' }
[pscustomobject]@{ File = $image.Name; Bytes = $image.Length; SHA256 = $actual }
```

Compare the byte count to the table for these exact files. A mismatch calls for checking the release, edition, language and download completeness, then reacquiring from the publisher as needed. Record source, version, filename, byte length, expected and actual digests, comparison date and Ubuntu signature status. Mark local media verified only after the applicable checks pass.

## Windows guest prerequisites

For WIN-ENDPOINT configure UEFI firmware, Secure Boot and virtual TPM 2.0 in the selected VMware release. The approved guest allocation of 2 vCPU, 6 GiB RAM and an 80 GiB growable disk meets Microsoft's minimum VM resource figures of two virtual processors, 4 GB and 64 GB. Confirm supported guest/host CPU and VMware version before boot. Protect the VM encryption/recovery information in the owner's private store. [Windows 11 VM requirements](https://learn.microsoft.com/en-us/windows/whats-new/windows-11-requirements)

Attach verified media to the intended guest's virtual CD/DVD drive during its approved deployment phase. Deploy SOC-SRV with the Ubuntu image first.
