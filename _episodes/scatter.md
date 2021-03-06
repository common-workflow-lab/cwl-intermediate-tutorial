---
title: "Scattering"
teaching: 0
exercises: 0
questions:
- "Key question (FIXME)"
objectives:
- "explain what is meant by the _scatter_ pattern in workflow design, and how it differs from the similar concept of parallel execution"
- "identify when the scatter pattern appears in a workflow description"
- "(FIXME: does the above cover the intended meaning of the following two points from the lesson development sprint?); running the same program on each file; running the same program the same way except for one parameter"
keypoints:
- "First key point. Brief Answer to questions. (FIXME)"
---
By the end of this episode,
learners should be able to
__implement scattering of steps in a workflow__.

# TODO Add pictures of cross product / matrix manipulation

> ## Exercise 1
>
> What if you had two arrays,
> one a file array of bams and an array of chromosomes?
> How would you run all chromosomes on each bam?
>
> > ## Solution:
> > 
> > ~~~
> > steps:
> >   GATK_HaplotypeCaller:
> >     run: GATK_HaplotypeCaller.cwl
> >     scatter: [intervals, input_bam]
> >     scatterMethod: flat_crossproduct
> > ~~~
> > {: .language-yaml }
> {. solution}
{. challenge}

> ## Exercise 2
>
> How does this change the inputs and outputs for the workflow?
>
> > ## Solution:
> >
> > ~~~
> > cwlVersion: v1.0
> > class: Workflow
> > requirements:
> >   ScatterFeatureRequirement: {}
> > inputs:
> >    bam: File
> >    chromosomes: string[]
> > outputs:
> >   HaplotypeCaller_VCFs:
> >     type:
> >       type: array
> >       items:
> >         type: array
> >         items: File
> >     outputSource: GATK_HaplotypeCaller/vcf
> > steps:
> >   GATK_HaplotypeCaller:
> >     run: GATK_HaplotypeCaller.cwl
> >     scatter: [intervals, input_bam]
> >     scatterMethod: flat_crossproduct
> >     in:
> >       input_bam: bam
> >       intervals: chromosomes
> >     out: [vcf]
> > ~~~
> > {: .language-yaml }
> {. solution}
{: .challenge}

When scattering on multiple inputs,
you need to explicitly say how the scatter should occur.
There are 3 scatter methods in CWL:
`dot_product`, `flat_crossproduct` and `nested_crossproduct`.
`dot_product` is the default method,
which takes each element of the array and runs on each nth item of the array.
`flat_crossproduct` and `nested_crossproduct` will take both inputs and
run on every combination of both arrays.
The difference between flat and nested is in the output type.
Flat will create a single array output whereas
nested will create a nested array output.

{% include links.md %}
