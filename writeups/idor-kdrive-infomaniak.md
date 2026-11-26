# IDOR in kDrive API — Access to Other Users' Private Files

**Program:** Infomaniak (YesWeHack)
**Date:** November 2026
**Severity:** Medium (CVSS 5.4)
**Type:** Insecure Direct Object Reference (CWE-639)
**Status:** Submitted

## Summary

The kDrive API endpoint `/api/3/drive/{drive_id}/files/last_modified` does not verify that the authenticated user owns the specified `drive_id`. Any authenticated Infomaniak user can list files in another user's **private** drive space.

## Methodology

### 1. Recon

- Registered two accounts (A = attacker, B = victim)
- Browsed the manager panel (manager.infomaniak.com/v3/)
- Identified API endpoints with user-specific IDs in the URL path
- Found drive_id in kDrive API calls

### 2. Testing

- Sent a request to B's drive endpoint using A's session cookie
- Kept B's drive_id in the URL
- Server returned 200 OK with B's private file listing

### 3. Key Evidence

- File in /Private/ path
- visibility: "is_in_private_space"
- created_by = B's user ID
- Capabilities: can_read: true, can_write: true, can_delete: true

## What I Learned

- Server-side rendered apps (SSR) are harder for IDOR hunting — data is baked into HTML
- SPAs with separate APIs are the best IDOR targets
- A 200 with empty data does not equal a bug. You need actual data in the response to prove impact
- 302 redirects on other endpoints confirmed the bug was specific to one endpoint (stronger report)

## Lessons

1. Check the target architecture before committing time
2. You need data in Account A to prove impact
3. One good finding > five vague ones
4. Don't waste trial reports on unconfirmed findings   