# Hypothesis Testing

Type 1 Error (False Positive):

This occurs when the model incorrectly rejects a true null hypothesis (H0 is true, but the model predicts H1).

Type 2 Error (False Negative):

This happens when the model fails to reject a false null hypothesis (H0 is false, but the model predicts H0).

## Smoke Alarm Example

**Ho : There is no fire**\
**Ha : There is fire**

Type 1 Error (False Positive):

Think of it as a "False Alarm." Imagine a smoke detector in your house. A Type 1 error would be when the smoke detector goes off (alarm sounds) even though there is no actual fire. In this case, you falsely believe there is a problem when there isn't. Type 2 Error (False Negative):

Think of it as a "Missed Opportunity." Continuing with the smoke detector example, a Type 2 error would be when the smoke detector fails to sound the alarm even though there is a real fire. In this case, you miss detecting the actual problem.

These real-world scenarios can help you associate Type 1 errors with false alarms and Type 2 errors with missed opportunities. Remembering them in this context might make it easier to recall which is which.

## Judge and Defendant Example

**Ho: Defendant is Innocent**\
**Ha: Defendant is Guilty**

Type 1 Error : Defendant is pronounced guilty, when in reality he is not guilty. In this case Ho was actually true but Judge said Ho is false.

Type 2 Error :Defendant is pronounced innocent, when in reality he was guilty. In this case Ho was actually false but Judge said Ho is true.

## Pregnancy Example

**Ho: Patient not pregnant**\
**Ha: Patient pregnant**

Type 1 Error : Telling a man that he is pregnant\
Type 2 Error : Telling a pregnant woman that she is not pregnant

## Simple Explanation

Type 1 error occurs when model says that Ho is false and Ha is true when in reality Ho is true and Ha is false.

Type 2 error occurs when model says that Ho is true and Ha is false when in reality Ho is false and Ha is true.

### Images to understand

{% embed url="https://www.google.com/imgres?imgurl=https://cdn1.qualitygurus.com/wp-content/uploads/2022/12/Type-I-and-Type-II-Errors.png?lossy%3D1%26resize%3D771%252C452%26ssl%3D1&tbnid=qxGle7wfo-gQnM&vet=1&imgrefurl=https://www.qualitygurus.com/type-i-and-type-ii-errors-explained/&docid=My68vboHYq5WOM&w=771&h=452&itg=1&source=sh/x/im/m1/2" %}

### Precision vs Recall Mindset

A system with high precision might leave some good items out, but what it returns is of high quality

A system with high recall might give you a lot of duds, but it also returns most of the good items



Precision: Book recommended out of all good books will be best book

Recall: Burgular robing a house will take everything including useless things
