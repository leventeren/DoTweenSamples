https://copyprogramming.com/howto/csharp-dotween-sequence-join-append-code-example

# Delayed Action

DOVirtual.DelayedCall (0.5f, ()=>Foo());
DOVirtual.DelayedCall (0.5f, ()=>Debug.Log("called after 0.5 sec."));

# Blink UGUI Image

Image image = this.gameObject.GetComponent<Image> ();

DOTween.ToAlpha (
    () => image.color,
    color => image.color = color,
    0.2f,  // target alpha.
    2.0f   // time.
).SetLoops (-1, LoopType.Yoyo).SetEase (Ease.InOutCirc);

# Infinite Loop UGUI Object

RectTransform rt = progressObj.transform as RectTransform;
rt.DORotate (new Vector3 (0.0f, 0.0f, -360.0f), 1.0f, RotateMode.LocalAxisAdd).SetLoops (-1, LoopType.Restart).SetEase(Ease.Linear);

# To

DOTween.To(
  () => GetSomeFloat(),
  (x) => SetSomeFloat(x),
  TargetSomeFloatValue,
  TweenDurationTime
);

Generic ways to create Tween:
- DOTween.To(MyMethod, startValue, endValue, time);
- var myFloat = 0f;
  DOTween.To(()=> myFloat, x=> myFloat = x, endValue, time);

# Sequence

mySequence = DOTween.Sequence();
mySequence.Append(this.transform.DOMoveY(MoveDistance, DownDoneTime).SetRelative().SetDelay(StartWaitTime).SetEase(Ease.Linear))
.Append(
this.transform.DOMoveY(startPosY, UpDoneTime).SetDelay(UpWaitTime).SetEase(Ease.Linear))
.SetDelay(TestValue); ;
mySequence.SetLoops(-1, LoopType.Restart);

// Grab a free Sequence to use
Sequence mySequence = DOTween.Sequence();
// Add a movement tween at the beginning
mySequence.Append(transform.DOMoveX(45, 1));
// Add a rotation tween as soon as the previous one is finished
mySequence.Append(transform.DORotate(new Vector3(0,180,0), 1));
// Delay the whole Sequence by 1 second
mySequence.PrependInterval(1);
// Insert a scale tween for the whole duration of the Sequence
mySequence.Insert(0, transform.DOScale(new Vector3(3,3,3), mySequence.Duration()));

# Re-use DOTween tweeners

Tweener playerMoveUpTweener = player.transform.DOLocalMove(new Vector3(0,100,0), 0.5f, true);
    playerMoveUpTweener.SetEase (Ease.Linear);
    playerMoveUpTweener.SetRelative (true);
    playerMoveUpTweener.SetRecyclable (true);
    playerMoveUpTweener.OnComplete(() => {
        // some stuff
    });

playerMoveUpTweener.Play();

Solution:

playerMoveUpTweener.SetAutoKill(false);
playerMoveUpTweener.Restart();

# DOTween: Tween As Task

public static class TweenExtensions
{
    public static Task AsTask(this Tween tween, CancellationToken cancellationToken)
    {
        var taskCompletionSource = new TaskCompletionSource<bool>();

        tween.OnComplete(() => { taskCompletionSource.TrySetResult(true); });
        tween.OnKill(() => { taskCompletionSource.TrySetCanceled(); });

        if (cancellationToken != CancellationToken.None)
        {
            tween.OnUpdate(() =>
            {
                if (cancellationToken.IsCancellationRequested)
                {
                    tween.Kill();
                }
            });
        }
           
        return taskCompletionSource.Task;
    }
}
