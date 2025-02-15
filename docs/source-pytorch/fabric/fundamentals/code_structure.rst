:orphan:

######################################
How to structure your code with Fabric
######################################

Fabric is flexible enough to adapt to any project structure, regardless of whether you are experimenting with a simple script or a big framework, because it makes basically no assumptions on how your code is organized.
Despite the ultimate freedom, this page is meant to give beginners a template for how to organize a typical training script with Fabric:
We also have several :ref:`examples <Fabric Examples>` that you can take inspiration from.


----------


*****************
The Main Function
*****************

At the highest level, every Python script should contain the following boilerplate code to guard the entry point for the main function:

.. code-block:: python

    def main():
        # Here goes all the rest of the code
        ...


    if __name__ == "__main__":
        # This is the entry point of your program
        main()


This ensures that any kind of multiprocessing will work properly (for example ``DataLoader(num_workers=...)`` etc.)


----------


**************
Model Training
**************

Here is a skeleton for training a model in a function ``train()``:

.. code-block:: python

    import lightning as L


    def train(fabric, model, optimizer, dataloader):
        # Training loop
        model.train()
        for epoch in range(num_epochs):
            for i, batch in enumerate(dataloader):
                ...


    def main():
        # (Optional) Parse command line options
        args = parse_args()

        # Configure Fabric
        fabric = L.Fabric(...)

        # Instantiate objects
        model = ...
        optimizer = ...
        train_dataloader = ...

        # Set up objects
        model, optimizer = fabric.setup(model, optimizer)
        train_dataloader = fabric.setup_dataloaders(train_dataloader)

        # Run training loop
        train(fabric, model, optimizer, train_dataloader)


    if __name__ == "__main__":
        main()


----------


*****************************
Training, Validation, Testing
*****************************

Often it is desired to evaluate the ability for the model to generalize on unseed data.
Here is how the code would be structured if we did that periodically during training (called validation) and after training (called testing).


.. code-block:: python

    import lightning as L


    def train(fabric, model, optimizer, train_dataloader, val_dataloader):
        # Training loop with validation every few epochs
        model.train()
        for epoch in range(num_epochs):
            for i, batch in enumerate(train_dataloader):
                ...

            if epoch % validate_every_n_epoch == 0:
                validate(fabric, model, val_dataloader)


    def validate(fabric, model, dataloader):
        # Validation loop
        model.eval()
        for i, batch in enumerate(dataloader):
            ...


    def test(fabric, model, dataloader):
        # Test/Prediction loop
        model.eval()
        for i, batch in enumerate(dataloader):
            ...


    def main():
        ...

        # Run training loop with validation
        train(fabric, model, optimizer, train_dataloader, val_dataloader)

        # Test on unseed data
        train(fabric, model, test_dataloader)


    if __name__ == "__main__":
        main()



----------


************
Full Trainer
************

Coming soon.
