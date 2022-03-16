---
layout: post
title:  "테스트 세트 구성방안 생각해보기"
categories: [ datascience ]
tags: [ basic ]
fullview: false
comments: true
---


unseen data로 평가해야 한다.

중복 제거가 그래서 중요하다.

![img](https://github.com/qqplot/qqplot.github.io/blob/main/assets/images/%EB%B6%80%EB%8F%99%EC%86%8C%EC%88%98%EC%A0%90.png?raw=true)

https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.TimeSeriesSplit.html

argument와 parameter 차이점
http://taewan.kim/tip/argument_parameter/


단어	번역	의미
Parameter	매개변수	함수와 메서드 입력 변수(Variable) 명
Argument	전달인자, 인자	함수와 메서드의 입력 값(Value)

```python
class TimeSeriesSplit(_BaseKFold):
    """Time Series cross-validator
    Provides train/test indices to split time series data samples
    that are observed at fixed time intervals, in train/test sets.
    In each split, test indices must be higher than before, and thus shuffling
    in cross validator is inappropriate.
    This cross-validation object is a variation of :class:`KFold`.
    In the kth split, it returns first k folds as train set and the
    (k+1)th fold as test set.
    Note that unlike standard cross-validation methods, successive
    training sets are supersets of those that come before them.
    Read more in the :ref:`User Guide <time_series_split>`.
    .. versionadded:: 0.18
    Parameters
    ----------
    n_splits : int, default=5
        Number of splits. Must be at least 2.
        .. versionchanged:: 0.22
            ``n_splits`` default value changed from 3 to 5.
    max_train_size : int, default=None
        Maximum size for a single training set.
    test_size : int, default=None
        Used to limit the size of the test set. Defaults to
        ``n_samples // (n_splits + 1)``, which is the maximum allowed value
        with ``gap=0``.
        .. versionadded:: 0.24
    gap : int, default=0
        Number of samples to exclude from the end of each train set before
        the test set.
        .. versionadded:: 0.24
    Notes
    -----
    The training set has size ``i * n_samples // (n_splits + 1)
    + n_samples % (n_splits + 1)`` in the ``i`` th split,
    with a test set of size ``n_samples//(n_splits + 1)`` by default,
    where ``n_samples`` is the number of samples.
    """

    def __init__(self, n_splits=5, *, max_train_size=None, test_size=None, gap=0):
        super().__init__(n_splits, shuffle=False, random_state=None)
        self.max_train_size = max_train_size
        self.test_size = test_size
        self.gap = gap

    def split(self, X, y=None, groups=None):
        """Generate indices to split data into training and test set.
        Parameters
        ----------
        X : array-like of shape (n_samples, n_features)
            Training data, where `n_samples` is the number of samples
            and `n_features` is the number of features.
        y : array-like of shape (n_samples,)
            Always ignored, exists for compatibility.
        groups : array-like of shape (n_samples,)
            Always ignored, exists for compatibility.
        Yields
        ------
        train : ndarray
            The training set indices for that split.
        test : ndarray
            The testing set indices for that split.
        """
        X, y, groups = indexable(X, y, groups)
        n_samples = _num_samples(X)
        n_splits = self.n_splits
        n_folds = n_splits + 1
        gap = self.gap
        test_size = (
            self.test_size if self.test_size is not None else n_samples // n_folds
        )

        # Make sure we have enough samples for the given split parameters
        if n_folds > n_samples:
            raise ValueError(
                f"Cannot have number of folds={n_folds} greater"
                f" than the number of samples={n_samples}."
            )
        if n_samples - gap - (test_size * n_splits) <= 0:
            raise ValueError(
                f"Too many splits={n_splits} for number of samples"
                f"={n_samples} with test_size={test_size} and gap={gap}."
            )

        indices = np.arange(n_samples)
        test_starts = range(n_samples - n_splits * test_size, n_samples, test_size)

        for test_start in test_starts:
            train_end = test_start - gap
            if self.max_train_size and self.max_train_size < train_end:
                yield (
                    indices[train_end - self.max_train_size : train_end],
                    indices[test_start : test_start + test_size],
                )
            else:
                yield (
                    indices[:train_end],
                    indices[test_start : test_start + test_size],
                )

```
