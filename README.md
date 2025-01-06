# Classifying the space groups of a given point group _(in the context of studying compact hyperKähler 8-orbifolds)_

This repository accompanies the paper "On Classifying HyperKähler Kummer 8-Orbifolds" - B.S.Acharya, D.A.Baldwin.

In section 3.3, there are three tables with some rows higlighted blue and some rows starred. The blue rows correspond to groups G such that $C^4/G$ is known to admit a symplectic resolution. The starred rows correspond to G such that $C^4/G$ is not known to admit a symplectic resolution. 

These correspond to the folders 'Table 1', 'Table 2' and 'Table 3'. Within each of these folders there are two subfolders: 'Blue Rows' and 'Starred Rows'. Within these there are files named by their row number.

Each file contains three variables: ```matgens_t_r``` ```translations_t_r``` and ```fixpointsets_t_r``` where ```t``` is the table number and ```r``` the row number. So, e.g. for row 71 of table 1, we have ```matgens_1_71``` etc.

## ```matgens_t_r```

This variable is a list of length $k$. Each element of the list is a matrix in $GL(8,Z)$ and together these matrices generate a point group $G$ which preserves the intersection form of the $E_{8}$ lattice. In GAP this is implemented as,
```
G := Group(matgens_t_r);;
```

## ```translations_t_r```

This variable contains a list of all the representatives of the classes of equivalent vector systems, each element of this list defines a space group $\hat{G}$ which is a representative of one of the affine equivalence classes of space groups with point group $G$ (or if there is a comment at the top of the file ```#no normaliser computed``` then ```translations_t_r``` is a list of representatives of the classes of strongly equivalent vector systems and some of the space groups will be in the same affine equivalence class). Each element is a list of length $8k$ which contains the translational parts of each of the generators. i.e. choosing the $i$-th vector system, the translation associated to the $j$-th generator in ```matgens_t_r``` is given by,

```
trans_gen1 := translations_t_r[ i ]{ [ 1 + 8*j .. 8 + 8*j ] };;
```

One can build each space group $\hat{G}$ in GAP using the CRYST package: 
```
LoadPackage("CRYST")

TransMats := [];;
for i in [1..8] do
	vec := NullMat(1,8)[1];
	vec[i] := 1;
	mat := IdentityMat(8);
	Add(mat,vec);
	mat := TransposedMatMutable(mat);
	Add(mat,[0,0,0,0,0,0,0,0,1]);
	Add(TransMats, mat);
od;

matgens_spacegrp := [];;
for i in [1..Length(matgens_t_r)] do
  gen := matgens_t_r[i];
	vec := translations_t_r[ i ]{ [ 1 + 8*j .. 8 + 8*j ] };
	mat := TransposedMatMutable(ShallowCopy(gen));
	Add(mat,vec);
	mat := TransposedMatMutable(mat);
	Add(mat,[0,0,0,0,0,0,0,0,1]);
	Add(matgens_spacegrp, mat);
od;
Append(matgens_spacegrp, TransMats);;

G_hat := AffineCrystGroupOnLeft(matgens_spacegrp);;
```

We then computed the singular set of $R^{8}/\hat{G}$. To do this we found the function ```WyckoffPositions``` in the Cryst package very useful and wrote a function that takes $\hat{G}$ as input and outputs information on the singular set.

## ```fixpointsets_t_r```

This is a list of the same length as ```translations_t_r```. The $i$-th element contains information about the singular set of $R^{8}/\hat{G}$, where $\hat{G}$ is the space group built using ```translations_t_r[i]```. It is a list of the form,
```[Small Group ID of stabiliser group, gens_of_stabiliser, "n fixpoints", "codimension c"]```
Here, ```gens_of_stabiliser``` is a list of words in the generators ```matgens_t_r``` where ```Fi``` corresponds to ```matgens_t_r[i]```, together these generate the stabiliser group $K$. The last two entries mean there are $n$ copies of $(C^{c}/K)\times T^{(8-c)}$ in the singular set.


**Comments welcome** 
