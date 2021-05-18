- 👋 Hi, I’m @Escha-Lee
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...

<!---
Escha-Lee/Escha-Lee is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->

void WindowsRSI::DrawLine(const Vector2& InStartPos, const Vector2& InEndPos, const LinearColor& InColor)
{
	Vector2 clippedStart = InStartPos;
	Vector2 clippedEnd = InEndPos;
	Vector2 screenExtend = Vector2(_ScreenSize.X, _ScreenSize.Y) * 0.5f;
	Vector2 minScreen = -screenExtend;
	Vector2 maxScreen = screenExtend;
	if (!CohenSutherlandLineClip(clippedStart, clippedEnd, minScreen, maxScreen))
	{
		return;
	}

	ScreenPoint startPosition = ScreenPoint::ToScreenCoordinate(_ScreenSize, clippedStart);
	ScreenPoint endPosition = ScreenPoint::ToScreenCoordinate(_ScreenSize, clippedEnd);

	int width = endPosition.X - startPosition.X;
	int height = endPosition.Y - startPosition.Y;

	bool isGradualSlope = (Math::Abs(width) >= Math::Abs(height));	//
	int dx = (width >= 0) ? 1 : -1;
	int dy = (height > 0) ? 1 : -1;
	int fw = dx * width;
	int fh = dy * height;

	int D = (isGradualSlope == true) ? fh * 2 - fw : 2 * fw - fh;
	int x = startPosition.X;
	int y = startPosition.Y;

	if (isGradualSlope)
	{
		while (x != endPosition.X)
		{
			SetPixel(ScreenPoint(x, y), InColor);

			if (D < 0)
			{
				D += 2*fh;
			}
			else
			{
				y += dy;
				D += 2 * (fh - fw);
			}
			x += dx;
		}
	}
	else
	{
		while (y != endPosition.Y)
		{
			SetPixel(ScreenPoint(x, y), InColor);

			if (D < 0)
			{
				D += 2 * fw;
			}
			else
			{
				D += 2 * (fw - fh);
				x += dx;
			}

			y += dy;
		}
	}
}
