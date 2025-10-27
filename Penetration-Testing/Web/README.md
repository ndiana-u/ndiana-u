# Web Penetration Testing

# IDOR Booking Enumeration — Project

## Objective
Demonstrate and verify an Insecure Direct Object Reference (IDOR) in a fast-food site's booking API by showing that base64-encoded, predictable order IDs can be manipulated to retrieve other users’ booking details.

## Tools used
- Burp Suite (Proxy, Repeater, Decoder, Encoder)  
- Browser (to create the booking)  

## Steps taken
1. Created a booking through the site UI and captured the HTTP response with Burp Proxy.  
2. Located the returned `order_id` in the JSON response (base64 string).  
3. Decoded the `order_id` to its numeric form (e.g., `MTIzNDU=` → `12345`) using Burp Decoder / `base64` / Python.  
4. Manipulated the numeric ID (in my test: decremented by 2 → `12343`).  
5. Re-encoded the modified numeric ID to base64 (e.g., `12343` → `MTIzNDM=`).  
6. Replaced the original `order_id` with the new base64 value and resent the request in Burp Repeater.  
7. Received a response containing another user’s booking details (full name, email, booking date), confirming the IDOR.

## Outcome / What I learned
- **Outcome:** Confirmed a high-impact IDOR: base64 encoding alone did not prevent enumeration and the API returned PII for arbitrary, guessable IDs.  

- **Lessons learned:**
  - Base64 is obfuscation, not security—never treat it as an access control.
  - Sequential/predictable numeric IDs enable easy enumeration if server-side ownership checks are missing.
  - Always enforce server-side authorization (verify resource ownership) and prefer unguessable IDs (UUIDs or signed tokens).
  - Limit PII returned by APIs and add rate-limiting, monitoring, and automated tests for access control.

> **Note:** Only perform these steps on systems you are authorized to test. Sanitize all artifacts before public sharing.
