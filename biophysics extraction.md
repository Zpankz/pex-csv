``` python
import dspy
from pydantic import BaseModel, Field
from dspy_toon import ToonAdapter
from typing import Optional, Literal

# Domain-specific entity models
class BiophysicalConcept(BaseModel):
    """Core biophysical principle or phenomenon"""
    name: str
    category: Literal[
        "mechanics", "thermodynamics", "fluid_dynamics", 
        "electricity", "gas_laws", "optics", "radiation"
    ]
    definition: str
    mathematical_expression: Optional[str] = None
    units: Optional[str] = None

class ClinicalApplication(BaseModel):
    """How a concept applies in critical care"""
    concept_name: str
    clinical_context: str
    monitoring_parameter: Optional[str] = None
    normal_range: Optional[str] = None
    pathophysiology: Optional[str] = None

class QuantitativeRelationship(BaseModel):
    """Mathematical/physical relationships between variables"""
    equation_name: str
    variables: list[str]
    relationship_type: Literal["proportional", "inverse", "exponential", "logarithmic", "complex"]
    formula: str
    clinical_significance: str

class MeasurementTechnique(BaseModel):
    """Methods for measuring biophysical parameters"""
    technique_name: str
    measured_parameter: str
    physical_principle: str
    limitations: Optional[str] = None
    accuracy_considerations: Optional[str] = None

# Comprehensive extraction signature
class ExtractBiophysicsConcepts(dspy.Signature):
    """Extract biophysical concepts, clinical applications, quantitative relationships, 
    and measurement techniques from critical care medicine biophysics text."""
    
    text: str = dspy.InputField(desc="Primary exam biophysics text passage")
    concepts: list[BiophysicalConcept] = dspy.OutputField(
        desc="Core biophysical principles mentioned"
    )
    clinical_applications: list[ClinicalApplication] = dspy.OutputField(
        desc="How concepts apply to patient care"
    )
    quantitative_relationships: list[QuantitativeRelationship] = dspy.OutputField(
        desc="Mathematical/physical relationships and equations"
    )
    measurement_techniques: list[MeasurementTechnique] = dspy.OutputField(
        desc="Methods for measuring relevant parameters"
    )

# Configuration
lm = dspy.LM("openai/gpt-4o-mini")
dspy.configure(lm=lm, adapter=ToonAdapter())

# Extractor instance
biophysics_extractor = dspy.Predict(ExtractBiophysicsConcepts)

# Example usage with critical care biophysics text
sample_text = """
Fick's Law of Diffusion states that the rate of gas transfer across a membrane 
is directly proportional to the surface area and partial pressure gradient, and 
inversely proportional to membrane thickness. In the alveolar-capillary interface, 
oxygen diffusion is measured using DLCO (diffusing capacity), which assesses both 
membrane conductance and capillary blood volume. Normal DLCO ranges from 75-125% 
predicted. In ARDS, reduced surface area and increased membrane thickness (due to 
edema) impair gas exchange.

Laplace's Law (P = 2T/r) explains alveolar stability: pressure within a sphere 
equals twice the surface tension divided by radius. Without surfactant, smaller 
alveoli would collapse into larger ones. Pulmonary compliance (ΔV/ΔP), measured 
via pressure-volume curves during mechanical ventilation, reflects lung elasticity. 
Static compliance typically ranges 50-100 mL/cmH₂O.
"""

result = biophysics_extractor(text=sample_text)

# Display extracted information
print("=== BIOPHYSICAL CONCEPTS ===")
for concept in result.concepts:
    print(f"• {concept.name} ({concept.category})")
    print(f"  Definition: {concept.definition}")
    if concept.mathematical_expression:
        print(f"  Formula: {concept.mathematical_expression}")
    if concept.units:
        print(f"  Units: {concept.units}")
    print()

print("\n=== CLINICAL APPLICATIONS ===")
for app in result.clinical_applications:
    print(f"• {app.concept_name} → {app.clinical_context}")
    if app.monitoring_parameter:
        print(f"  Monitor: {app.monitoring_parameter}")
    if app.normal_range:
        print(f"  Normal: {app.normal_range}")
    print()

print("\n=== QUANTITATIVE RELATIONSHIPS ===")
for rel in result.quantitative_relationships:
    print(f"• {rel.equation_name}: {rel.formula}")
    print(f"  Type: {rel.relationship_type}")
    print(f"  Variables: {', '.join(rel.variables)}")
    print(f"  Clinical significance: {rel.clinical_significance}")
    print()

print("\n=== MEASUREMENT TECHNIQUES ===")
for tech in result.measurement_techniques:
    print(f"• {tech.technique_name}")
    print(f"  Measures: {tech.measured_parameter}")
    print(f"  Principle: {tech.physical_principle}")
    if tech.limitations:
        print(f"  Limitations: {tech.limitations}")
    print()
```
