# LICENSE-GUIDE.md  

## TODO: Licensing and Attribution for SeqWeb

---

### Where to Post CC-BY-SA-4.0 Notices and Attribution

**1.  In the seqwebdata Github repo (for data/content):**
- Create a top-level file named something like `DATA_LICENSE.md` or `NOTICE.md`.
- In this file, state clearly that all data/content provided by SeqWeb (including any OEIS-derived data) is licensed under [CC-BY-SA-4.0](https://creativecommons.org/licenses/by-sa/4.0/).
- Include attribution to OEIS, e.g.:

  > Portions of the data/content provided by SeqWeb are derived from the OEIS (Online Encyclopedia of Integer Sequences), © The OEIS Foundation Inc. Licensed under CC-BY-SA-4.0.

- You may also include a short excerpt of the license or, more commonly, a link to the full license text.

**2. On the seqweb.org Website:**
- Add a clear notice in the website footer or a dedicated “About” or “Licensing” page, such as:
  > “All data/content provided by seqweb.org is licensed under the Creative Commons Attribution-ShareAlike 4.0 International License (CC-BY-SA-4.0). Portions of the data are derived from the OEIS, © The OEIS Foundation Inc.”
- Link to the full license: https://creativecommons.org/licenses/by-sa/4.0/.

**3. For Machine-Accessible Services (e.g., SPARQL endpoint):**
- **HTTP Headers:** Include a `Link` header in HTTP responses, e.g.:
```
Link: https://creativecommons.org/licenses/by-sa/4.0/; rel=“license”
```
- **Service Description:** If your endpoint provides a service description (like a VoID or DCAT document), include the license URI and attribution in the metadata.
- **SPARQL Results:** Optionally, include a comment at the top of results (if your endpoint supports this), or provide a link to a licensing page in the endpoint’s HTML interface.

**4. General Principles:**
- The license requires that attribution and license information be provided “in any reasonable manner based on the medium, means, and context in which You Share the Licensed Material”.
- For web and API services, visible notices and machine-readable metadata are both considered reasonable and standard practice.

---

**Summary Table**

| Context                | Where/How to Post Notice                                                                                      |
|------------------------|--------------------------------------------------------------------------------------------------------------|
| GitHub repo            | `DATA_LICENSE.md` or `NOTICE.md` at repo root with CC-BY-SA-4.0 notice and OEIS attribution                 |
| Website                | Footer or dedicated page with license notice, OEIS attribution, and link to license                          |
| SPARQL endpoint/API    | HTTP `Link` header, service description metadata, and/or visible notice in HTML interface                     |

---

**References:**  
- [CC-BY-SA-4.0 legal code, Section 3](https://creativecommons.org/licenses/by-sa/4.0/legalcode.en)  
- [CC FAQ: Reasonable means, medium, and context](https://support.skillscommons.org/faqs/category/8creative-commons-requirement/)  
- [Practical guide to using CC licenses](https://meta.wikimedia.org/wiki/Open_Content_-_A_Practical_Guide_to_Using_Creative_Commons_Licences/The_Creative_Commons_licencing_scheme)  
- [License text inclusion advice](https://www.ibcs.com/creative-commons-faq/)

If you follow these practices, you will meet the requirements of CC-BY-SA-4.0 for both human and machine users.
