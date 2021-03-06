### Additional Guidance

The SDOHCC\_Observation\_ScreeningResponseBase\_1 profile allows individual Question and Answer pairs (QA pairs) from an SDOH screening questionnaire to be represented as Observation instances. The Question and Answer pairs from the screening questionnaire are also represented using SDC Questionnaire and SDC QuestionnaireResponse.

Many of the resources/profiles used for SDOH reference one another. The current flow is that QuestionnaireResponses result in Observations that are used as evidence for Conditions that are addressed by CarePlans and Goals which lead to ServiceRequests and Procedures.

The sections that follow provide additional guidance on some elements in the SDOHCC\_Observation\_ScreeningResponseBase\_1 profile.

**Observation.category**

To facilitate searching for SDOH screening response observations, Observation.category is sliced and requires the &quot;Social History&quot; and &quot;Survey&quot; codes from the FHIR &quot;Observation Category Codes&quot; value set.

**Observation.code**

Observation.code is currently bound to codes from the FHIR &quot;LOINC Codes&quot; value set with binding strength: Required. LOINC codes are required because Gravity intends to use only LOINC encoded Questionnaires to generate QuestionnaireResponses which, in turn, will generate Screening Response Observations. As the Gravity Project continues to refine content for the SDOH domains (e.g., food, housing, transportation etc.), the value set for Observation.code will be refined to LOINC codes that pertain to SDOH domains.

**Observation.subject**

Observation.subject is restricted to referencing Patient Resource.

Although the US Core Condition, CarePlan, Goal, and Procedure Profiles restrict Subject to US Core Patient Profile, US Core does not offer a generic Observation Profile. For this reason, SDOHCC\_Observation\_ScreeningResponseBase\_1 does not restrict Observation.subject to US Core Patient Profile. However, if an implementer is creating US Core profiles (e.g., US Core Condition, US Core Procedure, etc.) that require US Core Patient for Subject, then US Core Patient should also be used for Subject in other profiles (e.g., Observation, ServiceRequest, etc.) which do not currently specify US Core Patient.

**Observation.effective**

This element is constrained to dateTime or Period.

&quot;dateTime&quot; can be used to document that the observation is effective during the specified dateTime but cannot extend to future times. Additionally, dateTime does not confirm or negate the existence of the observation at any time other than the specified dateTime.

&quot;Period&quot; can be used to document that the observation is effective during the specified Period but does not confirm or negate the existence of the observation at any time other than the specified Period. Period can have a &quot;start&quot; and an &quot;end&quot; time. For this profile, &quot;start time&quot; and &quot;end time&quot; are mandatory.

**Observation.performer**

Some SDOH QA pairs on a screening questionnaire are not answered directly (e.g., asserted) by the individual completing the questionnaire. Rather, some QA pairs (e.g., the Hunger Vital Sign 88124-3 &quot;Food insecurity risk&quot;) may be derived from other QA pairs. For a ScreeningResponse Observation that is not answered directly, but rather derived from other QA pairs on a screener, Observation.performer should be left empty and Observation.derivedFrom should be used to reference the relevant ScreeningResponse Observation(s).

**Example:** 88121-9 Hunger Vital Signs [HVS]

For the LOINC Hunger Vital Sign™ Panel, an answer of &quot;often true&quot; or &quot;sometimes true&quot; to either or both of the Hunger Vital Sign questions (88122-7 or 88123-5) identifies a patient as at risk for food insecurity (i.e., the answer for 88124-3 is &quot;At risk&quot;, LA19952-3).
<table><tr><td><img src="Observation_performer.png" /></td></tr></table>

Thus, 88124-3 &quot;Food insecurity risk&quot; is not explicitly answered by the individual completing the questionnaire. In this case, Observation.performer should be left empty and Observation.derivedFrom should reference the Observations that represent the QA pairs for Hunger Vital Sign questions 88122-7 and 88123-5.

**Observation.value and Observation.dataAbsentReason**

Observation.value is currently bound to codes from the FHIR &quot;LOINC Codes&quot; value set. As the Gravity Project continues to refine content for the SDOH domains, the value set for Observation.value will be refined to the LOINC Normative Answer Lists for LOINC codes that address SDOH domains.

Of note, many LOINC Normative Answer Lists include codes that overlap with values in the FHIR value set for Observation.dataAbsentReason.

**Example:** LOINC code 82589-3

For LOINC code 82589-3 &quot;What is the highest level of school that you have finished?&quot;, the Normative Answer List LL5351-3 includes &quot;I choose not to answer this question&quot;. This LOINC answer code overlaps with &quot;asked-declined&quot; in the value set for Observation.dataAbsentReason.
<table><tr><td><img src="Observation_value_1.png" /></td></tr></table>

However, some LOINC Normative Answer Lists do not include codes that overlap with values in the FHIR value set for Observation.dataAbsentReason.

**Example:** LOINC Code 76437-3

For LOINC Code 76437-3 &quot;What is your main insurance?&quot;, the Normative Answer List does not include a code that overlaps with a code in the value set for Observation.dataAbsentReason (e.g., unknown, asked-declined, etc.).
<table><tr><td><img src="Observation_value_2.png" /></td></tr></table>

If the LOINC Normative Answer List for a LOINC code offers an adequate code for why an Observation.value may not be known, it should be used as the value for Observation.value.

If the LOINC Normative Answer List for a LOINC code does not offer an adequate code for why an Observation.value may not be known, a code from Observation.dataAbsentReason should be used in lieu of Observation.value.

FHIR already has a rule that: _dataAbsentReason SHALL only be present if Observation.value[x] is not present dataAbsentReason.empty() or value.empty()._ However, this means that Observation.value and Observation.dataAbsentReason are both optional elements since one or the other may be used. Therefore, a profile could validate without Observation.value and without Observation.dataAbsentReason. For this reason, the Gravity Observation profiles have added the FHIRPath rule that: _Observation.value or Observation.dataAbsentReason MUST be provided._

**Observation.derivedFrom**

Observation.derivedFrom should reference SDC QuestionnaireResponse which, in turn, will reference the Questionnaire from which a QA pair in an Observation is derived.

However, some SDOH QA pairs (e.g., HVS 88124-3 &quot;Food insecurity risk&quot;) are not answered directly (e.g., asserted) by the individual completing the screening questionnaire but rather are derived from other QA pairs on the screener. In that case, Observation.derivedFrom should also reference any relevant Screening Response Observations (using SDOHCC\_Observation\_ScreeningResponseBase\_1).

**Example:** 88121-9 Hunger Vital Signs [HVS]

For the LOINC Hunger Vital Sign™ Panel, an answer of &quot;often true&quot; or &quot;sometimes true&quot; to either or both of the Hunger Vital Sign questions (88122-7 or 88123-5) identifies a patient as at risk for food insecurity (i.e., the answer for 88124-3 is &quot;At risk&quot;, LA19952-3).
<table><tr><td><img src="Observation_performer.png" /></td></tr></table>

Thus, 88124-3 &quot;Food insecurity risk&quot; is not explicitly answered by the individual completing the questionnaire. Observation.derivedFrom should be used to reference the Observations that represent the QA pairs for Hunger Vital Sign questions 88122-7 and 88123-5.

In addition to referencing SDC QuestionnaireResponse and possibly SDOHCC\_Observation\_ScreeningResponseBase\_1, Observation.derivedFrom may reference DocumentReference (e.g., a paper form), Observation and/or QuestionnaireResponse.