---
title: Лекция 18 - Бинарно дърво 3
category: новини
tags:
  - лекции
  - материали
---

### Код от час

#### А клас
```c
```

#### Б клас
```c
#include <stdio.h>
#include <stdlib.h>


struct node_t
{
    int data;

    struct node_t* left;
    struct node_t* right;

};


struct node_t* create_node(int data)
{
    struct node_t* new_node = malloc(sizeof(struct node_t));

    new_node->data = data;
    new_node->left = NULL;
    new_node->right = NULL;

    return new_node;
}


void add_leaf(struct node_t *root, int data)
{
    if(root->data == data)
    {
        return;
    }

    if(root->data > data)
    {
        if (root->left == NULL) 
        {
            root->left = create_node(data);
        }
        else
        {
            add_leaf(root->left, data);
        }
        
        
    }
    else if(root->data < data)
    {

        if (root->right == NULL) 
        {
            root->right = create_node(data);
        }
        else
        {
            add_leaf(root->right, data);
        }
    }
    
}


int search(struct node_t *root, int data)
{
    if(root == NULL)
    {
        return 0;
    }
    if(root->data == data)
    {
        return 1;
    }
    else
    {
        if(root->data > data)
            return search(root->left, data);
        else
        {
            return search(root->right, data);
        }
    }
    

}


struct node_t* arr_to_tree(int *arr, int start, int end)
{

    if(start > end)
    {
        return NULL;
    }
    
    int mid = (start + end)/2;

    struct node_t *root = create_node(arr[mid]);

    root->left = arr_to_tree(arr, start, mid - 1);
    root->right = arr_to_tree(arr, mid + 1, end);

    return root;
}


void print_tree(struct node_t *root, int level)
{
    if(root == NULL)
    {
        return;
    }

    print_tree(root->left, level + 1);


    int i = 0;
    for(; i<level*2; i++)
        printf(" ");
    printf("%d\n", root->data);

    print_tree(root->right, level + 1);

}

void print_levels(struct node_t *root, int level)
{
    if(root == NULL)
    {
        return;
    }

    print_levels(root->left, level + 1);



    printf("--%d\n", level);

    print_levels(root->right, level + 1);

}



int sum_tree(struct node_t *root)
{
    if(root == NULL)
     return 0;

    int sum = root->data;
    sum += sum_tree(root->left);
    sum += sum_tree(root->right);
    
    return sum;
}



void free_tree(struct node_t *root)
{
    if(root == NULL)
    {
        return;
    }

    free_tree(root->left);
    free_tree(root->right);

    free(root);
}



struct node_t* remove_element(struct node_t *root)
{
    struct node_t *new = NULL;
    if(root->right == NULL)
    {
        new = root->left;
    }
    else
    {
        struct node_t ** new_min = &root->right;
        while((*new_min)->left != NULL)
        {
            new_min = &(*new_min)->left;
        }

        new = *new_min;
        *new_min = (*new_min)->right;

        new->left = root->left;
        new->right = root->right;
    }

    free(root);
    return new;
    
}

struct node_t* filter_even(struct node_t *root) 
{
    if(root == NULL)
        return root;
    
    if(root->data % 2 == 0)
    {
        root = filter_even(remove_element(root));
    }
    else
    {
        root->left = filter_even(root->left);
        root->right = filter_even(root->right);
    }
    
    return root;
}

int main()
{

    struct node_t* root =  create_node(10);
    add_leaf(root, 8);
    add_leaf(root, 12);
    add_leaf(root, 11);
    add_leaf(root, 13);
    add_leaf(root, 1);

    printf("56 : %d\n",search(root, 56));
    printf("11 : %d\n",search(root, 11));

    print_tree(root, 0);
    free_tree(root);

    int arr[10] = {1,2,3,4,5,6,7,8,9,10};
    struct node_t *new_root = arr_to_tree(arr, 0, 9);
    print_tree(new_root, 0);

    //new_root = remove_element(new_root);

    new_root = filter_even(new_root);
    print_tree(new_root, 0);

    //print_levels(new_root, 0);

    return 0;


    struct node_t **arr_str = malloc(sizeof(struct node_t*)*10);

    for(int i =0; i<10; i++)
        arr_str[i] = malloc(sizeof(struct node_t));

    for(int i =0; i<10; i++)
        free(arr_str[i]);

    free(arr_str);

}
```

#### В клас
```c
#include <stdio.h>
#include <stdlib.h>

struct point_t {
  int x;
  int y;
};

struct node_t {
  struct point_t* points;
  int point_count;
  struct node_t* left;
  struct node_t* right;
};

void sort_points(struct point_t* points, int size)  {
  for(int i = 0; i < size - 1; i++) {
    for(int j = i + 1; j < size; j++) {
      if(points[i].x > points[j].x) {
        struct point_t temp = points[i];
        points[i] = points[j];
        points[j] = temp;
      }
    }
  }
}
/*
?? insert(??) {
  ??
}
*/

struct node_t* init_node() {
  struct node_t* new_node = malloc(sizeof(struct node_t));

  new_node->points = NULL;
  new_node->left = NULL;
  new_node->right = NULL;
  new_node->point_count = 0;

  return new_node;
}

struct node_t* build_tree(struct point_t* points, int size, int limit) {
  sort_points(points, size);

  struct node_t* root = init_node();

  if(size <= limit) {
    root->points = realloc(root->points, sizeof(struct point_t) * size);
    for(int i = 0; i < size; i++) {
      root->points[i] = points[i]; // put in left
    }
    root->point_count = size;

    return root;
  }

  int half = size/2;
  root->left = build_tree(points, half, limit);
  root->right = build_tree(points +half, size - half, limit);

  /*
  //root->left->points = malloc(sizeof(struct point_t)* half);
  root->left->points = realloc(root->left->points, sizeof(struct point_t) * half);
  for(int i = 0; i < half; i++) {
    root->left->points[i] = points[i]; // put in left
  }
  root->left->point_count = half;

  root->right->points = realloc(root->right->points, sizeof(struct point_t) * (size - half));
  for(int i = half; i < size; i++) {
    root->right->points[i - half] = points[i]; // put in right
  }
  root->right->point_count = size - half;
  */
  return root;
}

void print_tree(struct node_t* root, int level) {
  if(root == NULL) return;

  printf("[%d] %d", level, root->point_count);

  if(root->left != NULL || root->right != NULL) {
    print_tree(root->left, level + 1);
    print_tree(root->right, level + 1);
    //puts("\n");
  }  
}



int main() {
  struct point_t points[] = {
    {1, 2},
    {2, 2},
    {7, 3},
    {5, 9},
    {4, 1}
  };

  struct node_t* root = build_tree(points, 5, 1);
  print_tree(root, 0);

  return 0;
}
```
