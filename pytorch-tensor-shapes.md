# Pytorch Tensor Shapes

```python
x = torch.randint(0, 10, (1, 3, 5))  # Values will be 0 to 9
em = nn.Embedding(100, 2)  # Embedding with vocab_size 100 and embedding_dim 2

output = em(x[0])
print("X :", x)
print("--"*20)
print("Output shape:", output.shape)
print("Output :", output)
```

Output

Notice the shape of embedding 1st 2 dimension is same as input's 2nd and 3rd dim. we can say that each input row is converted to a batch of size equal to number of features(5) and embeding dimension(2)

```
X : tensor([[[9, 2, 7, 5, 8],
         [6, 8, 0, 6, 6],
         [1, 9, 7, 8, 5]]])
----------------------------------------
Output shape: torch.Size([3, 5, 2])
Output : tensor([[[-0.3330, -1.0566],
         [ 0.0844,  0.8795],
         [ 0.3895, -0.0850],
         [ 0.3029, -3.0282],
         [ 0.5957,  0.4328]],

        [[-1.4674, -0.6537],
         [ 0.5957,  0.4328],
         [-1.3697, -2.1440],
         [-1.4674, -0.6537],
         [-1.4674, -0.6537]],

        [[-0.3171,  1.6504],
         [-0.3330, -1.0566],
         [ 0.3895, -0.0850],
         [ 0.5957,  0.4328],
         [ 0.3029, -3.0282]]], grad_fn=<EmbeddingBackward0>)
```



```python
x = torch.randint(0, 10, (2, 3, 5))  # Values will be 0 to 9
em = nn.Embedding(100, 2)  # Embedding with vocab_size 100 and embedding_dim 2

output = em(x[0])
print("X :", x)
print("--"*20)
print("Output shape:", output.shape)
print("Output :", output)
```

Output

```
X : tensor([[[0, 0, 6, 1, 7],
         [0, 1, 4, 7, 8],
         [0, 2, 3, 2, 2]],

        [[1, 5, 9, 5, 1],
         [7, 3, 7, 2, 3],
         [0, 1, 9, 2, 1]]])
----------------------------------------
Output shape: torch.Size([3, 5, 2])
Output : tensor([[[-0.8984,  0.9534],
         [-0.8984,  0.9534],
         [ 1.5472,  0.4761],
         [ 0.1619, -0.3839],
         [ 0.9034,  0.4409]],

        [[-0.8984,  0.9534],
         [ 0.1619, -0.3839],
         [-0.6406,  0.8670],
         [ 0.9034,  0.4409],
         [ 0.7268, -0.2206]],

        [[-0.8984,  0.9534],
         [ 0.1042, -0.5466],
         [-0.9524,  0.1458],
         [ 0.1042, -0.5466],
         [ 0.1042, -0.5466]]], grad_fn=<EmbeddingBackward0>)
```
