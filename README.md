# ADHD Ontology

## OWLDoc
The ontology documentation can be accessed through this <a href="https://singular-arithmetic-c13420.netlify.app/" target="_blank">link</a>.

## ORSD

### Purpose
The ontology is designed to provide a formal representation of knowledge related to Attention Deficit Hyperactivity Disorder (ADHD), supporting healthcare professionals in the diagnostic process.
By systematically capturing the diagnostic criteria and symptomatology, the ontology seeks to enhance diagnostic accuracy and reduce the risk of false positives.
This structured framework allows clinicians to assess symptoms and criteria more precisely, making it a valuable tool for ensuring that diagnoses are aligned with established standards, such as those outlined in the DSM-5.
Additionally, the ontology can facilitate better communication between professionals, offering a shared understanding of ADHD that can improve patient outcomes.

### Scope
The ontology encompasses medical and psychological knowledge related to ADHD, including formal definition of ADHD, symptoms, 
criteria-based diagnosis (DSM-5), therapeutic and pharmacological approaches and age differences in development and manifestation of symptoms.

### Implementation Languages
The ontology was implemented using OntoUML and OWL.

### Intended End-Users
___User 1.___ Health professionals (psychiatrists, psychologists, neurologists).

___User 2.___ Medical and educational institutions.

___User 3.___ Researchers and academics involved in the study of ADHD.

___User 4.___ Developers of medical diagnostic support systems.

___User 5.___ Patients and family members (indirectly, through professionals who use the ontology).

### Intended Uses
___Use 1.___ Assist in research into ADHD and its clinical diagnosis.

___Use 2.___ Record processes related to medical evaluations and treatment prescriptions.

### Competency Questions

___CQ1.___ What are the main symptoms of ADHD in children, teenagers and adults?

___CQ2.___ What are the diagnostic criteria for ADHD according to the DSM-5?

___CQ3.___ How can ADHD symptoms vary between patients?

___CQ4.___ What are the commonly recommended treatments for ADHD?

___CQ5.___ What are the factors considered to establish severity levels for the disorder and how does the patient improve over time?

### Non-Functional Requirements
___NFR1.___ The ontology must be based on DSM-5.

___NFR2.___ The ontology must be compatible with Protegé software for editing and maintenance.

___NFR3.___ The ontology must be expressed in OWL and ensure interoperability with other healthcare ontologies.

### Functional Requirements
___FR1.___ The ontology must allow the representation of ADHD symptoms based on different age groups.

___FR2.___ The ontology must be aligned with the DSM-5 diagnostic criteria.

___FR3.___ There must be a clear distinction between pharmacological and therapeutic treatments.

___FR4.___ It must be possible to make inferences based on information about the patient's history to aid in diagnosis.

## Reference Ontology Visions Overview

### Clinical Evaluation
The clinical evaluation view models the clinical diagnosis process and the actors involved in it, in addition to defining characteristics of patients and medical conditions.

<p align="center">
  <img src="https://github.com/user-attachments/assets/d925a562-9d5a-4625-965d-ecc78a516159" />
</p>

### Diagnostic Criteria
The diagnostic criteria view models the diagnostic criteria for ADHD as specified in the DSM-5.

<p align="center">
  <img src="https://github.com/user-attachments/assets/4b491da8-5eed-4aa2-87c8-6b9c265fa5e6" />
</p>

### Treatment
The treatment models the most common treatments for ADHD, as well as the processes and actors involved in their prescription and execution.

<p align="center">
  <img src="https://github.com/user-attachments/assets/c8d9d1a6-8c9b-4188-a26d-1e6b5f526cee" />
</p>


## SPARQL Queries

### Query 1
The following query retrieves data for all patients.

```
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://example.com#>

SELECT ?patientName ?document ?dateOfBirth
WHERE {
	?patient rdf:type :Patient.
	OPTIONAL { ?patient :hasName ?patientName.        }
	OPTIONAL { ?patient :hasDocument ?document.       }
	OPTIONAL { ?patient :hasDateOfBirth ?dateOfBirth. }
}
```

### Query 2
The following query retrieves all patients who have received diagnoses and from which doctors. This query can be easily modified to filter patients or doctors.

```
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://example.com#>
PREFIX gufo: <http://purl.org/nemo/gufo#>

SELECT DISTINCT
	(STR(?patientName) 	AS ?patient_name)
	(STR(?doctorName) 	AS ?doctor_name)
WHERE {
	?patient rdf:type :Patient;
			 :hasName ?patientName.
	
	?report rdf:type :MedicalReport;
			gufo:mediates ?patient.
	
	?doctor rdf:type :Doctor;
			^gufo:mediates ?report;
			:hasName ?doctorName.
}
```

### Query 3
The following query retrieves all patients with predominantly hyperactive type ADHD.
Because of the way it is implemented, running without MINUS also returns combined type patients.
This query can be easily modified to look for patients with predominantly inattentive or combined types of ADHD.

```
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://example.com#>
PREFIX gufo: <http://purl.org/nemo/gufo#>

SELECT (STR(?patientName) AS ?patient_name)
WHERE {
	?patient rdf:type :Patient;
	         :hasName ?patientName.
	
	?report rdf:type :MedicalReport;
	        gufo:mediates ?patient;
	        gufo:mediates ?condition.
	
	?condition rdf:type :PredominantlyHyperactive.
    MINUS { ?condition rdf:type :PredominantlyInattentive. }
}
```

### Query 4
The following query retrieves all patients undergoing behavioral therapy and the corresponding psychologists.

```
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://example.com#>
PREFIX gufo: <http://purl.org/nemo/gufo#>

SELECT DISTINCT
	(STR(?patientName)      AS ?patient_name)
	(STR(?psychologistName) AS ?psychologist_name)
WHERE {
	?patient rdf:type :Patient;
	         :hasName ?patientName.
	
	?therapy rdf:type :BehavioralTherapy;
	         gufo:inheresIn ?patient;
	         gufo:externallyDependsOn ?psychologist.

	?psychologist :hasName ?psychologistName.
}
```

### Query 5
The following query retrieves all patients taking medication therapy, the medications prescribed, the dosages, and the prescribing doctors.

```
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://example.com#>
PREFIX gufo: <http://purl.org/nemo/gufo#>

SELECT DISTINCT
	(STR(?patientName)  AS ?patient_name)
	?medicine
	(STR(?dosage)       AS ?medicine_dosage)
	(STR(?doctorName)   AS ?doctor_name)
WHERE {
	?patient rdf:type :Patient;
	         :hasName ?patientName.
	
	?therapy rdf:type :MedicationTherapy;
	         gufo:inheresIn ?patient;
	         gufo:externallyDependsOn ?medicine.

	OPTIONAL { ?medicine :dosage ?dosage. }

	?prescription :prescribes ?therapy;
	              gufo:mediates ?patient;
		          gufo:mediates ?doctor.

	?doctor rdf:type :Doctor;
		    :hasName ?doctorName.
}
```

### Query 6
The following query recovers all patients undergoing some type of therapy, whether medication or behavioral.
In cases of medication therapy, the medication is returned, and in cases of behavioral therapy, the responsible psychologist is returned.

```
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://example.com#>
PREFIX gufo: <http://purl.org/nemo/gufo#>

SELECT DISTINCT
	(STR(?patientName) AS ?patient_name)
	(?medicine AS ?medication_therapy)
	(STR(?psychologistName) AS ?behavioral_therapy)
WHERE {
	?patient rdf:type :Patient;
	         :hasName ?patientName.
	
	OPTIONAL {
		?medicationTherapy rdf:type :MedicationTherapy;
		                   gufo:inheresIn ?patient;
		                   gufo:externallyDependsOn ?medicine.
	}
	
	OPTIONAL {
		?behavioralTherapy rdf:type :BehavioralTherapy;
		                   gufo:inheresIn ?patient;
		                   gufo:externallyDependsOn ?psychologist.

		?psychologist :hasName ?psychologistName.
	}

	{ ?medicationTherapy gufo:inheresIn ?patient. }

	UNION

	{ ?behavioralTherapy gufo:inheresIn ?patient. }
}
```
