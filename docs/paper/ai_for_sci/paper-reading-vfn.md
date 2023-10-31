!!! abstract
    There are some keypoints of the vector field networks(i.e VFN).

    * The VFN is en encoder which uses Graph networks to encode the feature of atoms.

    * VFN solved the atom representation bottleneck.

    * Every atom has learnable weiget, so the real atoms can be treated as virtual atoms, which is good to the model performance.

    * No need to use externel knowledge base.

## Previous work
The encoder used in the previous protein design and inversing folding task is IPA eocoder, which is derived from Alphafold.

The main problem of IPA encoder is that it uses pooling operation and the single pooled distance value to represent the atom feature. So that the IPA network cannot provide learnable weights for the coordinates of each atom, facing the **==atom representation bottleneck==**.

## Pipeline of the VFN

Firstly I want to introduce the common paradigm of de novo protein design. I think
the explaination in the original paper is really clear and easy to understand.

!!! quote
    The paradigm that combines protein structure diffusion models with inverse folding networks. Specifically, this paradigm initiates by employing a protein diffusion model to stochastically generate the backbone structure of represented by residue frames. Since the types of amino acids in the generated protein are initially unknown, an inverse folding network is then utilized to design the protein sequences for each residue based on the backbone residue frames. While this paradigm brings huge success for protein design, it also brings challenges for deep learning-based protein structure encoders.

```mermaid
graph LR
    A[Vector field operator] --> B[node interaction]
    B --> C[edge interaction]
    C --> D[virtual atom updating]
```

## Experiment 
!!! info
    The VFN achieve the SoTA results under the following tasks:
    
    * Pretein Diffusion

        * Diversity

        * Designability


    * Inverse folding 

        * Sequence recovery

        * Structure recovery

        * Speed and accuracy trade-off 