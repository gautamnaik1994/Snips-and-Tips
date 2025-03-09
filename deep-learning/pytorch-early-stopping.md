# Pytorch Early Stopping

#### **Basic Early Stopping Implementation in PyTorch**

```python
import torch

class EarlyStopping:
    def __init__(self, patience=5, min_delta=0.001):
        """
        Args:
            patience (int): How many epochs to wait after last time validation loss improved.
            min_delta (float): Minimum change in the monitored quantity to qualify as an improvement.
        """
        self.patience = patience
        self.min_delta = min_delta
        self.best_loss = float('inf')
        self.counter = 0

    def __call__(self, val_loss):
        if val_loss < self.best_loss - self.min_delta:
            self.best_loss = val_loss
            self.counter = 0  # Reset patience counter
        else:
            self.counter += 1  # Increase counter if no improvement

        return self.counter >= self.patience  # Return True if early stopping condition met

# Example Usage in a Training Loop
early_stopping = EarlyStopping(patience=5, min_delta=0.001)

for epoch in range(100):  # Example max epochs
    train_loss = train_model()  # Your training function
    val_loss = validate_model()  # Your validation function

    print(f"Epoch {epoch+1}, Train Loss: {train_loss}, Val Loss: {val_loss}")

    if early_stopping(val_loss):
        print("Early stopping triggered!")
        break
```

***

#### **Early Stopping with Model Checkpointing & Best Weights Restoration**

```python
import torch
import os

class EarlyStopping:
    def __init__(self, patience=5, min_delta=0.001, save_path="best_model.pth"):
        """
        Args:
            patience (int): How many epochs to wait after the last improvement.
            min_delta (float): Minimum improvement to reset patience.
            save_path (str): Path to save the best model.
        """
        self.patience = patience
        self.min_delta = min_delta
        self.save_path = save_path
        self.best_loss = float("inf")
        self.counter = 0
        self.best_model = None

    def __call__(self, val_loss, model):
        if val_loss < self.best_loss - self.min_delta:
            self.best_loss = val_loss
            self.counter = 0
            self.save_checkpoint(model)  # Save best model
        else:
            self.counter += 1

        if self.counter >= self.patience:
            print("Early stopping triggered!")
            self.restore_checkpoint(model)
            return True
        
        return False

    def save_checkpoint(self, model):
        """Save the model's state_dict."""
        torch.save(model.state_dict(), self.save_path)
        print(f"Checkpoint saved at {self.save_path}")

    def restore_checkpoint(self, model):
        """Load the best model's state_dict."""
        if os.path.exists(self.save_path):
            model.load_state_dict(torch.load(self.save_path))
            print("Restored best model weights.")

# Example Training Loop Usage
def train_model():
    return torch.randn(1).item() + 1  # Simulated train loss

def validate_model():
    return torch.randn(1).item()  # Simulated validation loss

model = torch.nn.Linear(10, 1)  # Example model
early_stopping = EarlyStopping(patience=3, min_delta=0.01)

for epoch in range(100):
    train_loss = train_model()
    val_loss = validate_model()

    print(f"Epoch {epoch+1}, Train Loss: {train_loss:.4f}, Val Loss: {val_loss:.4f}")

    if early_stopping(val_loss, model):
        break
```
