---
title: Merge sort
---

```c
#include <stdio.h>

#include <stdlib.h>


typedef char elmt;

int mgsrt( elmt *start, elmt *end );

int merge( elmt *start, elmt *mid, elmt *end );

int main()
{
    char a[]="7654321";
    printf("%d %s\n",mgsrt(a,a+6),a);
    return 0;
}

int mgsrt( elmt *start, elmt *end )
{
    int pairs=0;
    if( start<end )
    {
        elmt *mid=start+(end-start)/2;
        pairs   +=  mgsrt(start, mid);
        pairs   +=  mgsrt(mid+1, end);
        pairs   +=  merge(start, mid, end);
    }
    return pairs;
}

int merge( elmt *start, elmt *mid, elmt *end )
{
    int pairs=0;
    int i=0;
    elmt *temp=malloc(sizeof(elmt)*(end-start+1));
    elmt *left, *right;
    for( left=start,right=mid+1; left<=mid || right<=end; )
        *(temp+(i++))= ((left>mid || *left>*right)&&right<=end)? pairs+=(mid-left+1),*(right++):*(left++);
    for( i=0; i<=end-start; i++ )
        *(start+i)=*(temp+i);
    free(temp);
    return pairs;
}
```
