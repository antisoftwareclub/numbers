# cohost Numbers&trade;

Initial public release of [Numbers&trade; from cohost](https://cohost.org/staff/post/1254822-introducing-numbers).

There are minor differences between the code as released and what we were running in production. These differences are:
- remove some logic to pick a correct publish date and post ID when given a
  specific post; finding the correct publish date is the responsibility of
  whatever includes `<Numbers />` now.
  - this change is because we would have to release typedefs for the actual Post
    object, which is irrelevant for anyone other than us.
- change the Numbers&trade; display to use a style attribute instead of a
  tailwind class
  - adding a tailwind dependency would be silly. we are sparing y'all of that.

## "Algorithm" details

_for those who don't want to read the code_

The Numbers&trade; algorithm is very simple:

1. seed a random number generator off a post ID.
2. roll the dice four times
    - Numbers&trade; score
    - Negative score
    - Fractional score
    - Exponent score
3. Multiply the Numbers&trade; score by the number of seconds since/until the
   selected date.
    - For posts created _before_ April 1 at midnight UTC, this date is April 1
      at midnight UTC.
    - For posts created _after_ April 1 at midnight UTC, this date is the post
      publish date.
4. Check the Fractional score against a threshold
    - There is a 2% chance for a given post to have a decimal
    - If this check passes, normalize the roll from 0-0.02 to 0-1, lerp with this
      against our fraction digit range, floor the result. (1-5)
    - Use this when calling `toLocaleString` later.
5. Check the Exponent score against a threshold
    - There is a 50% chance for a given post to see exponential growth
    - If this check passes, normalize the roll from 0-0.5 to 0-1, lerp with this
      against our exponent range (1 - 1.42).
    - 1.42 was chosen solely because jae liked the aesthetics of the range that
      got us.
6. Check the Negative score against a threshold.
    - The Negative threshold shifts throughout the weekend, starting at 5% and
      capping at 60%.
    - Posts have a chance of becoming Negative at any time, although some rolls
      (>=0.6) will never go negative.
    - If this check passes, multiply the Number by -1.
7. Turn that shit into a locale string
    - This is where we actually use the fraction result.
8. Render it
    - When the user clicks the Number, we run all this again. Clicking DOES NOT
      increase the number, the number increases on its own by virtue of time
      having passed since you last clicked it.

## Use

You shouldn't use this. Reference dates are all hardcoded to April 1 because
that's when we released it. This release provided mostly for reference, but if
you _really_ want to use it:

```tsx
import {Numbers} from "@antisoftwareclub/numbers";

// your actual code goes here

<>
    <Numbers postId={69420} publishedAt="2022-02-03T08:30:00.000Z" />
</>
```

## Notes

- This does not include tests. If you want to write tests for it, knock yourself
  out.
- jae wrote this over the course of about two hours. set your code quality
  expectations accordingly.

## License

This release is licensed under an MIT license.

PLEASE NOTE: this license ONLY covers this release of `Numbers.tsx`. It DOES NOT
apply to any other part of the cohost code base.

## Note for us specifically

jae was too tired to get github actions to publish the package to npm so we've
gotta publish manually. this does not actually matter.